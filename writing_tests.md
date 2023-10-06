# writing tests

Now the real work starts...writing test plans is not just writing ansible code.  
You want your tests to mean something, so don't go writing tests like:  

'is that line in file x.y' if you wrote that in your ansible code, ansible will  
tell you that the line is there, by giving you a 'changed' or 'OK' status in your deployment  
play.  

Testing here means knowing the product / middleware / application and what it does,   
so your test must be a functional one.   

For example, is the deployment you opened a firewall port(80) for apache. Now you  
could test if the port is in the firewall config file (ERROR!) , you should test from  
a remote server if the port is open and if a service is listening on it, that is a functional  
test.

In a complex enviroment there is a lot to test, so make a layout of the environment to test and  
plot test scearios on this. Write the scenarios down in the README.md of the test repository, this  
way you and your team mates know what is tested.  

Breakup your environment into functional blocks, so you don't endup testing the complete enviroment   
in one big play, it will never be manageble. Make a testset for each block.  
As your platform probably has a layered structure, such as infrastructure / OS / middleware, try to create  
test repositories for each layer you want to test on.  
create an index of all tests, try not to repeat yourself, do not retest something that has already been   
tested in another layer.  

For each scenario, create a list of chonological steps to accomplish the test result.  
After the steps are clear, you add checks in between the steps to prove the result is what it needs to be.  
Once you have this, you can start writing the ansible tasks to test the scenario.  

## Best practice
Create a yml file for each scenario, so that all tasks for a certain scenario are in one file, with a   
descriptive filename in relation to the README.md  

Take your time planning and making this, it will save you eventually.  
Have your test play's reviewed ( I know, we all do that, right )  
Have the results reviewed.  
Report to management about the tests ( Make m happy )  

## File naming convention
The test framework needs no configuration for tests in scenario files, they just need to have a name  
that is in line with the naming convention....  
What is this naming convention?  

**`<run_stage>_<run_number>_<desciptive_name>.yml`**

The code will adapt to new files added to the directory / repository, it searches for files starting  
with `prep_<num>_*.yml` and `test_<num>_*.yml` and will include them into the main play.  

* **\<run_stage\>**
the framework knows 2 stages:

  - **prep**

  This is the preparation stage, here we checkout any role that are defined in all.yml.  
  We can do a syntax check on the role_to_test code  
  We check if all the defined credentials are there  
  You might add extra prep tasks just by adding a yaml file with a new, unique number..  
  Be aware, only add local tasks here, not remote, this is reserved for the test run.  
  So a new preparation task file might be named something like this:  

  prep_04_do_nothing.yml

  - **test**

  This is where the magic happens, you add as many test scenarios as you need and they  
  will be executed in the given order of the numbers of the scenarios/files.  

A typical test file is named:

test_10_check_firewall.yml

* **<run_number>**
This depicts the scenario number as you have identified them. It also depicts the order in which   
the framework will execute the tests.  
The numbers do not need to be sequentail, you may have some gaps to be filled in by other test files  
you will be adding later.  
Often scenarios are grouped and numbered as:  
  - 10-19 non-invasive tests
  - 20-29 invasive tests (like data changes..)
  - 30-39 destructive ( like killing processes/ stopping services )
  - ...
  - 90-99 recovery and cleanup (undo any data changes, remove files)

* **\<descriptive_name\>**

The name of the scenario, so everyone knows what is tested in this part of the play.  
For maintenance when there are a lot of yml files, it it convenient to have descriptive names.  

## The test code
Here is an example for a scenario, we will test a number of firewall ports on a system.  
As you can see in the codeblock below, a scenario is not a full playbook, it is just a   
collection of tasks, in a certain order, on certain hosts.  
While this is a verry simple example, it makes clear what we mean by functional testing.  

````yaml
---
#- name: "[AT-1O] Test if firewall ports are realy open"

- name: Check firewall ports
  ansible.builtin.wait_for:
    host: "{{ inventory_hostname }}"
    port: "{{ item }}"
    timeout: 10
  with_items:
    - 22
    - 80
    - 443
  delegate_to: "{{ another_host_in_in_env_to_check_from }}"
  when: ansible_hostname in test_hosts

- name: Setting fact to split later...
  ansible.builtin.set_fact:
    steps: |
      [AT-1O] Test that the firewall ports are really opened and something is listening
      Steps completed:
      1. Check firewall configuration for apache from remote host

- name: Acceptance test completed message
  ansible.builtin.debug:
    msg: "{{ steps.split('\n') }}"
````

As you can see, there are three tasks in this yml file, the first does the actual testing, the other 2  
are just for documentation purposes and give a nice output in logs. The first line documents the scenario  
in our case [AT-10] stands for 'Acceptance Test 10', which is one of the scenarios we defined in the test  
repos README.md.  
Keeping the scenario number in the filename makes it traceable, the descriptive name says it all.  

The numbers also depict the order in which the tests will be conducted, so we start at 01, 02,......

## Important Housekeeping
**Be sure to end each test scenario with a working situation!**

In a complex scenario, You might simulate failures by killing processes or stopping sevices on certain  
hosts. 

At the end of each scenario all things stopped must be running again, so the next scenario has   
the correct starting point for testing.  

**Be sure to keep track of these!**

You won't be the first to search for hours for a fault that was in the scenarios....  

[Run Your tests](run_m_all.md)

[Back](README.md)
