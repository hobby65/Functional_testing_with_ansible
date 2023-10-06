# Introduction
This document describes a testing environment in ansible that is not ment to check  
your code, but to test the functionality of the platform your code implements...  

Most organizations have linting and testing for ansible code in place, but in complex  
enviroments, testing of functionality is mostly done after delivery in the testing environment.  
Testing in development before delivery, can speedup delivery and help your department   
to deliver 'First Time Right' platforms.  
It also enables developers to do regression tests on changed platforms. Jusy run it again..  

An additional goal was, it has must run from the command line and in 'Tower or AWX', without modifications.  
This gives great power, not having to wait for the run to start in tower, just run it from   
the commandline.....  
Or shedule all tests on a regular basis, just to be sure..  

With that goal in mind, this was created.

## What does it do?
Out of the box? almost nothing...

Just the framework is in the repository, no tests that run on any platform  
It just does one thing 'out of the box', checkout your configured repository  
to a local directory and run a syntax check, the rest is up to you.  

If you use custom credentials in Ansible Automation Platform, it can check the   
presence of them before the run starts.  

If you use a vault role, it will also check it out and use the vars defined in them  

# What can it do?
Your creativity is the only limit this framework has....

Examples are:

## Oracle dataguard testing
Runnning a complete baseline test on an Oracle dataguard cluster, with switchovers,  
failovers, data retension tests and pluggable database verifications in between the  
scenarios.  
All in one run   

## Loadbalacer tests
A load balancer does nothing without an applicaion behind it, so we created a test platform with:  
````
2 apache servers
2 loadbalancer nodes (ipvs in this case)
````
The first test is a verification if it all works..  

After that the play orchestrates failures on all parts of the platform and verifies that the page  
on the site is still serviced.  

Only when all tests pass, the platform may be delivered.  
 
This gives assurance that your code is correct.  

## How do we implement this?
Read the next pages to find out more on how to implement the test plan.  

[Get You Copy into Git and configuration](download_and_git.md)

[writing test playbooks](writing_tests.md)

[Running your tests](run_m_all.md)

[The code explained](full_code.md)

Automate the hell out of IT...
 
