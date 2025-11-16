---
title: "Logging on to Setonix"
teaching: 5
exercises: 5
questions:
objectives:
- Learn how to remote access Setonix
- Learn about Pawsey-specific user shortcuts and their meanings
- Set up the working environment by downloading lesson materials
keypoints:
- Logging on to Pawsey systems uses SSH (secure shell)
---
### Getting your user names and passwords
We have created some training usernames and passwords for you. These are available on the google sheet linked on the PowerPoint slide. Go to that link and put your name next to one of the usernames to claim it for yourself. That will be your username and password for this whole workshop.

### Logging on to Setonix with Terminal or Windows PowerShell
Your user name and password will be supplied. Within a terminal window, type:

```bash
ssh username@setonix.pawsey.org.au
```
Enter your password when prompted. If asked to accept any credentials, type `yes` and hit enter

If you have successfully logged in, you should see your command prompt change

```output
username@setonix-01:~>
```

If you are unable to login, please first check your password was typed correctly. If you are still unable to login, please ask for assistance.


### Logging in using PUTTY (Windows)
Host name is `setonix.pawsey.org.au`

Port is `22`

User name is the name you selected on the training accounts sheet e.g. something like `cou001`

Helpful link: [https://www.hostinger.com/tutorials/how-to-use-putty-ssh](https://www.hostinger.com/tutorials/how-to-use-putty-ssh)

> ## **Important**
> - Login to Setonix with two separate terminals now.
>   - One will be used to execute workflows.
>   - The other will be used to browse outputs and monitor jobs.
> - Keep an additional local terminal for downloading result files.
{: .prereq}

### Useful shortcuts that Pawsey sets up for you
To save you some time typing, Pawsey has set up some shortcuts for all users. We will make use of these throughout the hands-on session. Let's look at them:


| Shortcut | Meaning |
|----------|----------|
| $USER | Your unique user ID. e.g. `cou001` or `sbeecroft` |
| $PAWSEY_PROJECT | Your default project code (some people are members of multiple projects). e.g. `courses` or `pawsey1086` |
| $MYSCRATCH | Path to your default scratch directory. e.g. `/scratch/courses/cou001/` |
| $MYSOFTWARE | Path to your default software directory. e.g. `/software/projects/courses/cou001` |


### Downloading the lesson material
There are some materials for the lessons which are hosted on github. In order to access those on Setonix, you will need to clone the git repo, then change directory. You can list the contents of the directory with `ls`

```bash
cd $MYSCRATCH
git clone https://github.com/tlitfin/2025-ABACBS-workshop
cd 2025-ABACBS-workshop/exercises/
ls
```

If you've successfully cloned the git repo, the `ls` command will return the following:

```
exercise1  exercise2  exercise3  exercise4
```
