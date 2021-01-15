## Security > Server Security Check > Console Guide

This section explains how to execute the check Agent.

## Procedure to execute Agent

Bring up the Agent execution script by selecting instance OS, check type and check standard.

![serversecuritycheck_01_20201015.png](https://static.toastoven.net/prod_serversecuritycheck/serversecuritycheck_01_20201015.png)

### Linux-family Agent

1\. Copy the execution script to the clipboard.

2\. Access the terminal of the target instance.

3\. Create and execute the Agent script as admin.

* Acquire root permissions.
* Create a script using an editor such as vi editor.
* Change the permissions of the created script file.
* Run the file.
```
[root@centos7 ~]# cd ~
[root@centos7 ~]# sudo su
[root@centos7 ~]# vi agent.sh
[root@centos7 ~]# chmod 744 agent.sh
[root@centos7 ~]# ./agent.sh
OS Security Check Success! :)
```

## Operation Inquiry

### Inquiry target

1\. Inquiries about failing to execute Agent

2\. Reports about the misuse of check results

### How to Inquire

1\. How to inquire **Customer Center > 1:1 Inquiry**

2\. Response time: Weekdays: 09:00-18:00
