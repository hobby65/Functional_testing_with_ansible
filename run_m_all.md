# Run all tests
Now we have created our testscenarios and plays, we would like to run them.  

Running the individual files is not efficient and, because they are not fully   
playbooks, not possible without another playbook to include them.  
This is where the power of the framework comes in, no need to edit configurations 
when a playbook is added, just add it to the repository and it will run.  

As motioned before, there are 2 ways of running this framework:
* on the commandline
* in Ansible Automation Platform

Before you want to run the tests in Ansible Automation Platform, you might want  
to verify if it is working.  

So we will start by running it manually:

* First You need to be sure you have all collections are installed that you use in the plays.
* Secondly, you need a clone of the inventory you want to use for testing ( NOT production!).
* Third, the machines to test with need to be reachable for your ansible machine, where you run this on.
* You need to have key authentication to the machines (maybe run as ansible user) 

make you current directory, that of the framework.  
Now run the command:  
````
ansible-playbook main.yml -i ../inventory/inventory_file -e test_hosts=\<test_node_name\> --ask-vault-pass
````

This will start the framework and run the test.

If this runs without problems, you can add the project to the automation controller and create a  
job template for the tests. Add the credentials needed and...  

Enjoy... sit back and just wait for it...  

## Output of the example play

When you run this exact play against a webserver that is only configured to listen on port 80, it will fail the run.  
So I have a problem in my scenario and should not test port 443.  

As you will see, the framework outputs much more than only the firewall task you have written, it outputs all  
tasks  

~~~
Vault password: 

PLAY [Prepare for testing] **********************************************************************************************

TASK [Set full repo URL from role_to_test] ******************************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
ok: [localhost] => {"ansible_facts": {"repo_name": "git@gitserver.localdomain:ivory_tower/os_rhel.git"}, "changed": false}

TASK [Create temp_group] ************************************************************************************************
changed: [web-apache1.localdomain] => {"add_host": {"groups": ["test"], "host_name": "web-apache1.localdomain", "host_vars": {"group": "test"}}, "changed": true}

TASK [Gather distribution facts] ****************************************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [Set os_fact] ******************************************************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [Gather date_time facts] *******************************************************************************************
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [Create directory for report file if not exist] ********************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [Write initial test fail to central repo file] *********************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [Find prepare tasks] ***********************************************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
ok: [localhost] => {"changed": false, "examined": 13, "files": [{"atime": 1678436877.3243928, "ctime": 1678436522.720951, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 1200434, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436484.713323, "nlink": 1, "path": "prep_01_get_roles_for_testing.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 613, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}, {"atime": 1678436877.3273928, "ctime": 1678436522.720951, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 1200436, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436484.713323, "nlink": 1, "path": "prep_02_syntax_code.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 613, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}, {"atime": 1678436877.3303928, "ctime": 1678436522.720951, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 1200435, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436484.713323, "nlink": 1, "path": "prep_03_check_credentials.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 358, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}], "matched": 3, "msg": "All paths examined", "skipped_paths": {}}

TASK [Include preparation tasks] ****************************************************************************************
skipping: [web-apache1.localdomain] => {"changed": false, "skip_reason": "Conditional result was False"}
included: /home/ansible/source/test/prep_01_get_roles_for_testing.yml for localhost => (item={'path': 'prep_01_get_roles_for_testing.yml', 'mode': '0664', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 613, 'inode': 1200434, 'dev': 64768, 'nlink': 1, 'atime': 1678436877.3243928, 'mtime': 1678436484.713323, 'ctime': 1678436522.720951, 'gr_name': 'ansible', 'pw_name': 'ansible', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': True, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': True, 'xoth': False, 'isuid': False, 'isgid': False})
included: /home/ansible/source/test/prep_02_syntax_code.yml for localhost => (item={'path': 'prep_02_syntax_code.yml', 'mode': '0664', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 613, 'inode': 1200436, 'dev': 64768, 'nlink': 1, 'atime': 1678436877.3273928, 'mtime': 1678436484.713323, 'ctime': 1678436522.720951, 'gr_name': 'ansible', 'pw_name': 'ansible', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': True, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': True, 'xoth': False, 'isuid': False, 'isgid': False})
included: /home/ansible/source/test/prep_03_check_credentials.yml for localhost => (item={'path': 'prep_03_check_credentials.yml', 'mode': '0664', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 358, 'inode': 1200435, 'dev': 64768, 'nlink': 1, 'atime': 1678436877.3303928, 'mtime': 1678436484.713323, 'ctime': 1678436522.720951, 'gr_name': 'ansible', 'pw_name': 'ansible', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': True, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': True, 'xoth': False, 'isuid': False, 'isgid': False})

TASK [Set the list fact first] ******************************************************************************************
ok: [localhost] => {"ansible_facts": {"roles_to_get": ["role_vault", "os_rhel"]}, "changed": false}

TASK [Checkout role from GIT source] ************************************************************************************
ok: [localhost] => (item=role_vault) => {"_extra_role": "role_vault", "after": "e65a777debd3147f099dd7b4f3957d37e4b504d9", "ansible_loop_var": "_extra_role", "before": "e65a777debd3147f099dd7b4f3957d37e4b504d9", "changed": false, "remote_url_changed": false}
ok: [localhost] => (item=os_rhel) => {"_extra_role": "os_rhel", "after": "3019903afae3336feb0cd56f71fb8c08a882f689", "ansible_loop_var": "_extra_role", "before": "3019903afae3336feb0cd56f71fb8c08a882f689", "changed": false, "remote_url_changed": false}

TASK [Checkout role os_rhel  from git] **********************************************************************************
ok: [localhost] => {"after": "3019903afae3336feb0cd56f71fb8c08a882f689", "before": "3019903afae3336feb0cd56f71fb8c08a882f689", "changed": false, "remote_url_changed": false}

TASK [Run syntax-check on the role] *************************************************************************************
ok: [localhost] => {"changed": false, "cmd": "ansible-playbook --syntax-check syntax.yml", "delta": "0:00:00.463179", "end": "2023-03-10 09:28:41.777357", "failed_when_result": false, "msg": "", "rc": 0, "start": "2023-03-10 09:28:41.314178", "stderr": "[WARNING]: provided hosts list is empty, only localhost is available. Note that\nthe implicit localhost does not match 'all'", "stderr_lines": ["[WARNING]: provided hosts list is empty, only localhost is available. Note that", "the implicit localhost does not match 'all'"], "stdout": "Using /home/ansible/source/test/ansible.cfg as config file\n\nplaybook: syntax.yml", "stdout_lines": ["Using /home/ansible/source/test/ansible.cfg as config file", "", "playbook: syntax.yml"]}

TASK [Fail if any of the credentials are missing] ***********************************************************************

PLAY [Run the tests] ****************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [web-apache1.localdomain]

TASK [Include vault role] ***********************************************************************************************

TASK [role_vault : Include vars] ****************************************************************************************
ok: [web-apache1.localdomain] => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result", "changed": false}

TASK [Find vars file] ***************************************************************************************************
ok: [web-apache1.localdomain -> localhost] => {"changed": false, "examined": 13, "files": [{"atime": 1678436884.486314, "ctime": 1678436522.720951, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 1200441, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436484.713323, "nlink": 1, "path": "vars.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 31, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}], "matched": 1, "msg": "All paths examined", "skipped_paths": {}}

TASK [Include vars for testing] *****************************************************************************************
ok: [web-apache1.localdomain] => (item={'path': 'vars.yml', 'mode': '0664', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 31, 'inode': 1200441, 'dev': 64768, 'nlink': 1, 'atime': 1678436884.486314, 'mtime': 1678436484.713323, 'ctime': 1678436522.720951, 'gr_name': 'ansible', 'pw_name': 'ansible', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': True, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': True, 'xoth': False, 'isuid': False, 'isgid': False}) => {"_vars": {"atime": 1678436884.486314, "ctime": 1678436522.720951, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 1200441, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436484.713323, "nlink": 1, "path": "vars.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 31, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}, "ansible_facts": {}, "ansible_included_var_files": ["/home/ansible/source/test/vars.yml"], "ansible_loop_var": "_vars", "changed": false}

TASK [Find test playbooks] **********************************************************************************************
ok: [web-apache1.localdomain -> localhost] => {"changed": false, "examined": 13, "files": [{"atime": 1678436884.749311, "ctime": 1678436522.720951, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 1200439, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436484.713323, "nlink": 1, "path": "test_01_include_role.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 307, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}, {"atime": 1678436884.752311, "ctime": 1678436767.3155923, "dev": 64768, "gid": 1000, "gr_name": "ansible", "inode": 52450174, "isblk": false, "ischr": false, "isdir": false, "isfifo": false, "isgid": false, "islnk": false, "isreg": true, "issock": false, "isuid": false, "mode": "0664", "mtime": 1678436767.3075924, "nlink": 1, "path": "test_10_check_firewall.yml", "pw_name": "ansible", "rgrp": true, "roth": true, "rusr": true, "size": 637, "uid": 1000, "wgrp": true, "woth": false, "wusr": true, "xgrp": false, "xoth": false, "xusr": false}], "matched": 2, "msg": "All paths examined", "skipped_paths": {}}

TASK [Include testcases] ************************************************************************************************
included: /home/ansible/source/test/test_01_include_role.yml for web-apache1.localdomain => (item={'path': 'test_01_include_role.yml', 'mode': '0664', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 307, 'inode': 1200439, 'dev': 64768, 'nlink': 1, 'atime': 1678436884.749311, 'mtime': 1678436484.713323, 'ctime': 1678436522.720951, 'gr_name': 'ansible', 'pw_name': 'ansible', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': True, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': True, 'xoth': False, 'isuid': False, 'isgid': False})
included: /home/ansible/source/test/test_10_check_firewall.yml for web-apache1.localdomain => (item={'path': 'test_10_check_firewall.yml', 'mode': '0664', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 637, 'inode': 52450174, 'dev': 64768, 'nlink': 1, 'atime': 1678436884.752311, 'mtime': 1678436767.3075924, 'ctime': 1678436767.3155923, 'gr_name': 'ansible', 'pw_name': 'ansible', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': True, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': True, 'xoth': False, 'isuid': False, 'isgid': False})

TASK [Display test name] ************************************************************************************************
ok: [web-apache1.localdomain] => {
    "msg": "Running TEST 01 - run role os_rhel from start to finish"
}

TASK [run the role to test] *********************************************************************************************

TASK [Run handlers after finishing role] ********************************************************************************

TASK [Check firewall ports] *********************************************************************************************
ok: [web-apache1.localdomain -> localhost] => (item=22) => {"ansible_loop_var": "item", "changed": false, "elapsed": 0, "item": 22, "match_groupdict": {}, "match_groups": [], "path": null, "port": 22, "search_regex": null, "state": "started"}
ok: [web-apache1.localdomain -> localhost] => (item=80) => {"ansible_loop_var": "item", "changed": false, "elapsed": 0, "item": 80, "match_groupdict": {}, "match_groups": [], "path": null, "port": 80, "search_regex": null, "state": "started"}
failed: [web-apache1.localdomain -> localhost] (item=443) => {"ansible_loop_var": "item", "changed": false, "elapsed": 10, "item": 443, "msg": "Timeout when waiting for web-apache1.localdomain:443"}

NO MORE HOSTS LEFT ******************************************************************************************************

PLAY RECAP **************************************************************************************************************
localhost                  : ok=9    changed=0    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0   
web-apache1.localdomain    : ok=9    changed=1    unreachable=0    failed=1    skipped=8    rescued=0    ignored=0   
~~~
If we remove the test for the HTTPS port, it will run nicely and you have verified the framework was implemented  
correctly.  
Last part of the output when run again with correct ports to test  
````
PLAY RECAP **************************************************************************************************************
localhost                  : ok=12   changed=0    unreachable=0    failed=0    skipped=8    rescued=0    ignored=0   
web-apache1.localdomain    : ok=12   changed=0    unreachable=0    failed=0    skipped=8    rescued=0    ignored=0   
````
You see that there are no failed tasks in the play, so all is well.


Have fun using this.

[Back](README.md)
