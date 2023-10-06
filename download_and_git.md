# Download and setup the template in GIT
In this repository, you will find a tarbal with the template code, just download and  
unpack it:  


````
tar xvzf test_plan.tgz
````

## get the template in git
Create a new repository in your project named 'test_plan_template' or something like it.  
Be sure to make this a template, not modifying the code. This is where you fork the other   
testing repositories from.   

that's all...  

Now you are almost ready to let your creativity take control..  

 
## Fork the template
Lets start simple...  
Say you want to test apache server in you development environment..  

Fork (or make a copy) the test_plan_template into a new repository named   
**apache_testing**.  
Clone the new repository to your work environment, using git clone.  

You now have a complete framework on your machine to test....   

well, nothing yet :-)  

The apache_testing repo needs to be configured for the environment  

## configuration

The framework uses a group_vars/all.yml file for configuration like:
````
role_to_test: ROLE_TO_TEST
repo_url: git@<gitserver>:<project>/
repo_version: master
report_host: <reportserver>
report_dir: /var/ansible/report/
report_file: report.csv
use_vault_role: 
  - role_vault

extra_roles: []

required_credentials: []
````
You might recognise some fields, but let me describe them for you anyway:  

* **role_to_test**  
The repository to be checked out from git and taken through the syntax checks and other  
testing. We will discuss this later..  
This can NEVER be empty or the play will fail.  
* **repo_url**  
The base url to your git project repository, the code will use this together with role_to_test  
to checkout the code from git into a subdirectory roles.  
* **repo_version**  
The branch/tag to checkout for the role_to_test, this gives the opportunity to test different versions of   
roles (later).  
* **report_host**  
A virtual machine that is configured for ansible, to write a record of testing to.  
This is done only when a test is performed from 'Tower' or 'Ansible Automation Plaform', so when a test is   
sheduled, you can trace back the job_id for this run and its result. This gives your tests tracebility  
for management reporting (you could upload the file data into splunk/grafana) to create dashboards.  
* **report_dir**  
The directory on the reporting server where the file will be written.  
* **report_file**  
The file that holds all records of the tests that have been run.  
Due to the current nature of the reporting, the code overwrites records on key values, if you don't want this  
just adapt the template accordingly and updated the exsisting forks.  
* **use_vault_role**  
If you are using vault, just state the vault repository here, it will be cloned, run and cleaned after the run.  
Be sure to add the vault credential to the play.  
* **extra_roles**  
Extra roles can be used to do configuration before testing..  
For example, create a pluggable database on an empty Oracle database platform you want to test.  
The role will be cloned into the roles directory, but you will have to call them from your plays yourself.  
At the end of the run, these roles a cleaned up.  
* **required_credentials**  
If you use custom credentials in your environment, you can insert the lookup variables here, so they will  
be checked before the run starts, this prevents failed or half-way runs.  

If you are happy with the configuration, save it and push it into git, so others can use it.  
The base config is now complete.  
You could run it now just so you know it runs without error.  
Before you do, review the README.md in the apache_test repository, there you will find how to run it.  


[write your tests](writing_tests.md)

[Back](README.md)
