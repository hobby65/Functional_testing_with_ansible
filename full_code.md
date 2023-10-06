# The full code review
In this section we explain the full code of the testing template, we take the main.yml and  
disect it section by section and explain why its there and what it does.  



## On which hosts are we testing?

As any other playbook it needs to know on which hosts the play will run.  
We passed the hosts to test on by giving the parameter 'test_hosts' with a host or group to test on.  
This will be used as hosts for the play, but we add the localhost to the play as well. We do that so we can report   
the result of the play to the reporting host from localhost.  
````yaml
---
- name: Prepare for testing
  hosts: "{{ [test_hosts] | flatten | join(',') }},localhost"
  any_errors_fatal: true
  gather_facts: false
````

## Initialize Variables

Before we do any tests, we need to initialize some variables zo we can find things, like a gi repository to checkout..  
We create a specal group 'test' with all the host of this play in it. We set the changed when_false on almost every  
task of the main.yml itself, so the play overview will have changes grom the role or test plays only.  
We also get the distribution_major_version from the targeted hosts, this is used in the report section.  
A fact is set with the inventory os_buildingblock_name and the major_ditrubution_version (eg. 'rhel8') fir use in the report  
record that will be written. This is done because you might want to test the same code against rhel8 and rhel9.  
````yaml
  pre_tasks:

    - name: Set full repo URL from role_to_test
      ansible.builtin.set_fact:
        repo_name: "{{ repo_url }}{{ role_to_test }}.git"
      when: inventory_hostname == 'localhost'

    - name: Create temp_group
      ansible.builtin.add_host:
        name: "{{ inventory_hostname }}"
        group: test
      run_once: true
      changed_when: false

    - name: Gather distribution facts
      ansible.builtin.setup:
        gather_subset: 'distribution_major_version'
      when:
        - tower_job_id is defined
        - inventory_hostname != 'localhost'

    - name: Set os_fact
      ansible.builtin.set_fact:
        bb_name: "{{ hostvars[groups['test'][0]]['os']['name'] }}{{ hostvars[groups['test'][0]]['ansible_distribution_major_version'] }}"
      when:
        - tower_job_id is defined
        - inventory_hostname == 'localhost'
````
## Report we have started the play
This section writes the record stating that the test-run has failed, this means here that it has started  
if the play breaks anywhere, you will find that it has failed.  
The report record will only be written if there is a job_id (eg. runs through tower or AWX), from the  
command line you can see it runnig obviously.  
````yaml
    - name: Gather date_time facts
      ansible.builtin.setup:
        gather_subset: 'date_time'
      when:
        - tower_job_id is defined 
        - inventory_hostname == 'localhost'

    - name: Create directory for report file if not exist
      ansible.builtin.file:
        path: "{{ report_dir }}"
        state: directory
        mode: '0755'
        recurse: true
      delegate_to: "{{ report_host }}"
      failed_when: false
      when:
        - tower_job_id is defined 
        - inventory_hostname == 'localhost'

    - name: Write initial test fail to central repo file
      ansible.builtin.lineinfile:
        path: "{{report_dir }}{{ report_file }}"
        regexp: "^{{ role_to_test }}, {{ repo_version }}, {{ hostvars['localhost']['bb_name'] }}"
        line: "{{ role_to_test }}, {{ repo_version }}, {{ hostvars['localhost']['bb_name'] }}, FAILED, {{ tower_job_id }}"
        state: present
        create: true
      delegate_to: "{{ report_host }}"
      when:
        - tower_job_id is defined
        - inventory_hostname == 'localhost'
````
## Find and run the 'preparation' tasks
The first taks is a find in the currnet directory on localhost for any files that match 'prep_\*.yml'.  
By default, this should find the following files:  
* prep_01_get_roles_for_testing.yml  
  This will checkout all the roles mentioned in group_vars/all.yml from the GIT repositories.  
* prep_02_syntax_code.yml  
  This will run a syntax-check on the syntax.yml, which will include the 'role_to_test'.  
* prep_03_check_credentials.yml  
  This checks if the defined custom credentials are valid, if any.  
 
````yaml
    - name: Find prepare tasks
      ansible.builtin.find:
        paths: "."
        patterns: 'prep*.yml'
      register: _preps
      when: inventory_hostname == 'localhost'

  tasks:
    - name: Include preparation tasks
      ansible.builtin.include_tasks: "{{ _prep.path }}"
      loop_control:
        loop_var: _prep
      with_items: "{{ _preps.files | sort(attribute='path') }}"
      when:
        - _preps.files is defined
        - inventory_hostname == 'localhost'
````
## Prepare for running the tests
In this section we include the vault role, so we have all passwords we need to run the tests.  
Additionally, we include any vars_files we can find so the vars needed for tests are availlable.  
Last we do a search on the current directory to find any test plays we have to run.  
````yaml
- name: Run the tests
  any_errors_fatal: true
  hosts: "{{ [test_hosts] | flatten | join(',') }},localhost"

  pre_tasks:

    - name: Include vault role
      ansible.builtin.include_role:
        name: "{{ use_vault_role | join('') }}"
      when: use_vault_role is defined

    - name: Find vars file
      ansible.builtin.find:
        paths: "."
        patterns: 'vars*.yml'
      register: _varsfiles
      delegate_to: localhost

    - name: Include vars for testing
      ansible.builtin.include_vars:
        file: "{{ _vars.path }}"
      loop_control:
        loop_var: _vars
      with_items: "{{ _varsfiles.files }}"
      when:
        - _varsfiles.files is defined
        - _varsfiles.matched > 0

    - name: Find test playbooks
      ansible.builtin.find:
        paths: "."
        patterns: 'test*.yml'
      register: _tests
      delegate_to: localhost
````
## Finally run the tests
So now we include all test files we found in the previous section and include them into  
the play to run them in sequence. We run this as a block, so we can always do a cleanup  
at the end of the play (eg. remove checked out roles).  
````yaml
  tasks:

    - name: Run as block 
      block:
        - name: Include testcases
          ansible.builtin.include_tasks: "{{ _test_case.path }}"
          loop_control:
            loop_var: _test_case
          with_items: "{{ _tests.files | sort(attribute='path') }}"
          when:
            - _tests.files is defined
            - inventory_hostname != 'localhost'

````
## Cleanup any mess
As the tests have run, we clean all roles we checked out, even if the tests failed.

````yaml
      always:
        - name: Remove vault role
          ansible.builtin.file:
            path: "roles/{{ use_vault_role | join('') }}"
            state: absent
          changed_when: false
          when:
            - use_vault_role is defined
            - inventory_hostname == 'localhost'

        - name: Remove extra roles
          ansible.builtin.file:
            path: "roles/{{ _extr_role }}"
            state: absent
          changed_when: false
          loop_control: 
            loop_var: _extr_role
          with_items: "{{ extra_roles }}"
          when: inventory_hostname == 'localhost'

        - name: Remove cloned role
          ansible.builtin.file:
            path: "roles/{{ role_to_test }}"
            state: absent
          changed_when: false
          when: inventory_hostname == 'localhost'

````
## Write final record, if we got here
When run through tower or AWX, update the report by replacing the line thet said failed  
with a line that says 'SUCCESS', so your report shows the correct result.  
When you read the file you will see the correct result, but beware...tools like splunk read every   
update and could show you the wrong results...You will have to tweak the report for this behavure.  
````yaml
    - name: "Write test result to central repo file"
      ansible.builtin.lineinfile:
        path: "{{ report_dir }}{{ report_file }}"
        regexp: "^{{ role_to_test }}, {{ repo_version }}, {{ hostvars['localhost']['bb_name'] }}"
        line: "{{ role_to_test }}, {{ repo_version }}, {{ hostvars['localhost']['bb_name'] }}, SUCCESS, {{ tower_job_id }}"
        state: present
      delegate_to: "{{ report_host }}"
      when: tower_job_id is defined
````

This is all there is...

Happy testing (and maybe happy managers)

