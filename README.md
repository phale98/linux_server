# linux_server

# FSND Linux Configuration

### Getting Started
1. Log into or create an AWS account.
1. Choose OS only.
1. Select the Ubuntu Distribution.
1. Go with the five dollar plan.
1. Name your server.
1. Click the create button.
1. Log inot the server with ssh.
1. Update the packages with `sudo apt-get update`
1. Upgrade the packages with `sudo apt-get upgrade`
1. Configure the time zone with `sudo dpkg-reconfigure`, UTC is under none of the above.

### Configuring OAuth
1. Go to Google Cloud Platform.
1. Under API & Services click Credentials
1. Edit the authorized JavaScript origins to inlcude the server's public ip address and the domain name,
   which can be found using a service like hcidata.info.
1. In Authorized Redirects make sure to include the /gconnect or other google connect method.

### Creating the grader user with sudo rights
1. Log into your new server.
2. Run `sudo adduser grader`
3. Fill out the prompts for creating the user, name grader, password grader.
4. `sudo touch /etc/sudoers.d/grader`
5. edit the file with `sudo vim /etc/sudoers.d/grader`
6. Press escape, then i. Add the line `grader ALL=(ALL:ALL) ALL`
6. Save and quit with esc and :wq.

### Enable SSH authentication
#### Local Machine (Not Server)
1. Open your termnial (Windows will have to use something like git bash or Cygwin).
2. Navigate to the users home directory `cd ~`
3. Use `ls -a` to see if you have a .shh directory, you dont use `mkdir .ssh` to create one.
4. Run  `ssh-keygen`
5. Type .ssh/[NAME OF KEY HERE] the name is arbitrary.
6. Accept with enter.
6. Use `cd .ssh` to enter directory and copy the output of `cat [NAME_OF_KEY].pub`
