# Prerequisites
* Jenkins master
* Another server runs linux-base OS, e.g. CentOS, RedHat

# Steps
## Generating an SSH key
Create a new SSH key if it's not existed on Jenkins master
### On Windows with PuTTYgen
* Open PuTTYgen and click "Generate" button with key type "SSH-2 RSA", download the public and private key files to local disk.

### On Linux with SSH command
* Open Terminal, and switch to the user who runs Jenkins service, in our case, it's "jenkins" user. This step is quite critical, because it makes sure the generated key file is authorized properly
* Paste the text below, substituting in your email address as comment   
`ssh-keygen -t rsa -b 2048 -C "your_email@example.com"`   
This creates a new ssh key, using the provided email as a label.   
`Generating public/private rsa key pair.`   
* When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location.   
Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]   
* At the prompt, type a secure passphrase.   
```
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```
### Check public key
The public key must be in one line and starts with "ssh-rsa", e.g.
```
ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAjHnf5dRJPCIADtGBSUR5kM5xCC+3vttHjsMekIzmKt0pdOf2RrI5zLP00OLcRyis2uYX/bqR4pwJH13M/5zPPyKFHWoxhH3pLDlP6IkYvT3U4H+00tN5Y6aSt8H7ae1q0tB6VhqWPvv+SS1L9eY7as9if3dJw0ZkrWYVBX5IlGtWZH0Uu3qsp1p3k+l600CghOVvGlYMAgzdewMPJaRiiiC05+TUZYEPj1QnFLiIZdnQUqx8rwUj8dMUAPjzFZZHFhEiPJjX5AC885n/WBRW/dIhlurzZvsUUoKWB5KYgi1JxNOz4qOD8IriXOwOwiYAedsv8rghfbb1VqPIC3PICQ== rsa-key-20150917
```

## Add Credential in Jenkins master
Create a new Credential for "jenkins" user if it's not existed on Jenkins master
* Login Jeknins as "admin" role
* Click "Manage Jenkins" and add a new credential
* Select "SSH Username with private key" kind
* Choose scope as "System (Jenkins and nodes only)"
* Enter username (in our case, "jenkins")
* Provide Private key, Input the private key (from above step) directly or select option "From the Jenkins master ~/.ssh"
* If your key has a passphrase, press "Advanced" and type in the passphrase
* Press Save to create new credential

## Create jenkins user on new slave server
Create a new user named as "jenkins" if it's not existed on Linux node, Use command `useradd` or `adduser` in Linux and other Unix-like operating systems, and set a non-empty password to unlock the user.
```
useradd jenkins
passwd jenkins
Changing password for user jenkins.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

## Create authorized_keys file on new slave server
After creating the "jenkins" user, switch to "jenkins" user, and create `authorized_keys` with public key (from above step) under `~\.ssh\` folder. Set proper permissions to jenkins user home directory and .ssh directory
```
$mkdir ~/.ssh
$chmod 700 ~/.ssh
$touch ~/.ssh/authorized_keys
$chmod 644 ~/.ssh/authorized_keys
$chown jenkins:jenkins ~/.ssh -R
```
The content of `authorized_keys` file contains several public keys, e.g. 
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCowagvhV6nP3XYuF05MQqZ2PnSTGHSm4Q3TMiPi4H4MmqfflklHwYtDk0FKld0imn9LcZA35uua4CCOFb4zFhYkLSnR9+V6/rbrTsvNuBJAXUFW2IUvx18rAoJNoGe/u8d4yuCNaD7lCjYmmV/wOBWD40nNbk8s5K/7TvKR2yyVdIUPMyWgyY9fdlMkfoJmR35cZ49G8N62S7Oa6K0eGti4LADynbaWUCHtgIieTRYGtHG1f77QFSPKuWK6Ewv30svDOaHSf2i996PVwPZkNckF4PnreEjJvFIdSXQy6VReN1qU4WW04FyUFApY+Kfve8Z1sDobdET8nKte2HwYH5L jenkins@pvgl50798672a.apj.global.corp.sap
```

## Add new node in Jenkins master
* Login Jeknins as "admin" role
* On the Home page, go to "Manage Jenkins", then "Manage Nodes"
* On the left, click "New Node"
* Enter a node name, then press "Dumb Slave", then OK
* Enter "1" as the number of executors (you probably don't want to build two devices at the same time)
* Enter your path to your jenkins user home in "Remote root directory" (e.g. `/home/jenkins`).
* Make sure Usage is set to "Only build jobs with label restrictions matching this node", and Launch method is set to "Launch slave agents on Unix machines via SSH"
* Below the Launch method drop-down, enter the slave's IP address in "Host"
* Choose the credential of "jenkins" user created in above step on Jenkins master
* Click "Save" again to create a node

# Troubleshooting
* How to trouble shoot when it still goes wrong?   
Enable the debug level log in file `/etc/ssh/sshd_config` with below change. You may need to restart sshd service to apply setting change.
```
LogLevel DEBUG3
```
Check the log from `/var/log/auth.log` or `/var/log/secure` on new Jenkins linux slave
* How to restart sshd in Red Hat 7 or CentOS 7
```
service sshd restart
```
* How To Fix "Server refused our key" Error in CentOS 6   
Refer to [link](https://adamwright.wordpress.com/2013/04/15/how-to-fix-server-refused-our-key-error-in-centos-6/), try below command `restorecon -R -v /home/jenkins/.ssh`
* How to generate random password for user
```
openssl rand -base64 6 | tee -a ~joe/.password | passwd --stdin joe
```
This will assign user a random password, and store the same password in ~joe/.password, where joe will be able to see it.
* How to add user to wheel group
```
# usermod -aG wheel USERNAME
```
