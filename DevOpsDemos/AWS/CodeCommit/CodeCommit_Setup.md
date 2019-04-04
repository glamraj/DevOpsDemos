##############
Welcome to AWS Code Commit
You can use the AWS Management Console and upload, add, or edit a file to a repository directly from the AWS CodeCommit console. This is a quick way to make a change. However, if you want to work with multiple files, files across branches, and so on, consider setting up your local computer to work with repositories. In this demo, we will learn how to setup AWS Code Commit using SSH and IAM roles.

Set Up SSH Connections to AWS CodeCommit Repositories

Follow this article in Youtube
Set Up SSH Connections to AWS CodeCommit Repositories
Note: This is for Linux/Mac users.

Create IAM Users/Groups
It is better to have a seperate groups(say Devs) and add your users to that group.

Add Group Permission - Managed Policy - AWSCodeCommitFullAccess

Create SSH Keys
# Create the `.ssh` directory if it isn't there already
# mkdir -p $HOME/.ssh
cd $HOME/.ssh
ssh-keygen
# [here just create the name codecommit_rsa and leave all fields blank *just click enter*]
cat codecommit_rsa.pub  
Associate Your Public Key with Your IAM User
Now we need to enter our codecommit_rsa.pub into AWS IAM.
Copy the SSH key ID (for example, APKAEIBAERJR2EXAMPLE)
Add AWS CodeCommit to Your SSH Configuration
cd $HOME/.ssh
touch config
chmod 600 config
cat > $HOME/.ssh/config << "EOF"
Host git-codecommit.*.amazonaws.com
  User YOUR_SSH_KEY_ID_FROM_IAM
  IdentityFile ~/.ssh/codecommit_rsa
EOF
Test your SSH configuration:
ssh git-codecommit.us-east-1.amazonaws.com
You should see something like this,

You have successfully authenticated over SSH. You can use Git to interact with AWS CodeCommit. Interactive shells are not supported.Connection to git-codecommit.us-east-1.amazonaws.com closed by remote host.

#################


[root@ip-172-31-26-126 ec2-user]# adduser glam
[root@ip-172-31-26-126 ec2-user]# passwd glam
Changing password for user glam.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[root@ip-172-31-26-126 ec2-user]# su glam
[glam@ip-172-31-26-126 ec2-user]$ ls -l
ls: cannot open directory .: Permission denied
[glam@ip-172-31-26-126 ec2-user]$ cd /home
[glam@ip-172-31-26-126 home]$ ls -l
total 0
drwx------. 4 ansadmin ansadmin 111 Mar 14 12:10 ansadmin
drwx------. 4 ec2-user ec2-user 111 Mar 13 15:57 ec2-user
drwx------. 2 glam     glam      62 Apr  4 17:09 glam
[glam@ip-172-31-26-126 home]$ cd glam
[glam@ip-172-31-26-126 ~]$ ls -l
total 0
[glam@ip-172-31-26-126 ~]$ pwd
/home/glam
[glam@ip-172-31-26-126 ~]$ ls -ld $HOME/.ssh
ls: cannot access /home/glam/.ssh: No such file or directory
[glam@ip-172-31-26-126 ~]$ mkdir -p $HOME/.ssh
[glam@ip-172-31-26-126 ~]$ cd $HOME/.ssh
[glam@ip-172-31-26-126 .ssh]$ ls -ld
drwxrwxr-x. 2 glam glam 6 Apr  4 17:26 .
[glam@ip-172-31-26-126 .ssh]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/glam/.ssh/id_rsa): codecommit_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in codecommit_rsa.
Your public key has been saved in codecommit_rsa.pub.
The key fingerprint is:
SHA256:l9i43LdijypVUfcBIjQ5nSW/+dahD9FRws8tthpSPi0 glam@ip-172-31-26-126.ap-south-1.compute.internal
The key's randomart image is:
+---[RSA 2048]----+
|        .+o++++..|
|         o+o+..oo|
|          .. . +o|
|         +... * =|
|        S.+o * = |
|       ..+. E * o|
|       .o ...B o.|
|      .   oo..+  |
|       ..o.oo  . |
+----[SHA256]-----+
[glam@ip-172-31-26-126 .ssh]$ ls -ld
drwxrwxr-x. 2 glam glam 54 Apr  4 17:27 .
[glam@ip-172-31-26-126 .ssh]$ cat codecommit_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCjM1UIp/km/NH8Xn5WWJDlYwYy/947lIYGXwGVPBvK8Tfv8FWMrWR1098D1HSWR9b3m5xxNh7EqeDVf03FEuxbzHk6w/sUenbjcQwfz4geuJAzHEslJ2PBg1UQPkhp2ZaMiZU1dGGwC+NWWd/jTJDjypD5Mwp+TGGGOEq2crYkVqkPdmBRzKnPgjLWkdvJ2S79BE14p+mpJBS8v1Uu5k6ZoiC51HIhrtKpQg5jbZzDT7+JfyQr/mXrJ4YROj7fsJ4R5XuGSnozXs10ONRQxJkN6TVQxikAo+mWluxhJ90/3Zxcre1l6R1vndBoDdkkwIXZE2fVdUR1hBPdVzR2VGzz glam@ip-172-31-26-126.ap-south-1.compute.internal
[glam@ip-172-31-26-126 .ssh]$ ls -l
total 8
-rw-------. 1 glam glam 1679 Apr  4 17:27 codecommit_rsa
-rw-r--r--. 1 glam glam  431 Apr  4 17:27 codecommit_rsa.pub
[glam@ip-172-31-26-126 .ssh]$ ^C
[glam@ip-172-31-26-126 .ssh]$ cd $HOME/.ssh
[glam@ip-172-31-26-126 .ssh]$ touch config
[glam@ip-172-31-26-126 .ssh]$ chmod 600 config
[glam@ip-172-31-26-126 .ssh]$ cat > $HOME/.ssh/config << "EOF"
> Host git-codecommit.*.amazonaws.com
>   User APKAROQIMQCXATQQDNWO
>   IdentityFile ~/.ssh/codecommit_rsa
> EOF
[glam@ip-172-31-26-126 .ssh]$ ls -l
total 12
-rw-------. 1 glam glam 1679 Apr  4 17:27 codecommit_rsa
-rw-r--r--. 1 glam glam  431 Apr  4 17:27 codecommit_rsa.pub
-rw-------. 1 glam glam  101 Apr  4 17:32 config
[glam@ip-172-31-26-126 .ssh]$ cat config
Host git-codecommit.*.amazonaws.com
  User APKAROQIMQCXATQQDNWO
  IdentityFile ~/.ssh/codecommit_rsa
[glam@ip-172-31-26-126 .ssh]$ ssh git-codecommit.us-east-1.amazonaws.com
The authenticity of host 'git-codecommit.us-east-1.amazonaws.com (52.94.229.29)' can't be established.
RSA key fingerprint is SHA256:eLMY1j0DKA4uvDZcl/KgtIayZANwX6t8+8isPtotBoY.
RSA key fingerprint is MD5:a6:9c:7d:bc:35:f5:d4:5f:8b:ba:6f:c8:bc:d4:83:84.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'git-codecommit.us-east-1.amazonaws.com,52.94.229.29' (RSA) to the list of known hosts.
You have successfully authenticated over SSH. You can use Git to interact with AWS CodeCommit. Interactive shells are not supported.Connection to git-codecommit.us-east-1.amazonaws.com closed by remote host.
Connection to git-codecommit.us-east-1.amazonaws.com closed.
[glam@ip-172-31-26-126 .ssh]$ ssh git-codecommit.us-east-1.amazonaws.com
You have successfully authenticated over SSH. You can use Git to interact with AWS CodeCommit. Interactive shells are not supported.Connection to git-codecommit.us-east-1.amazonaws.com closed by remote host.
Connection to git-codecommit.us-east-1.amazonaws.com closed.
[glam@ip-172-31-26-126 .ssh]$
