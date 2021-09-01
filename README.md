## Hand on with AWS
### Moving "app" VM to AWS
---
#### Initialising the machine on AWS
---
- Firstly, ensure that the selected server is Ireland, eu-west-1
- Initiliase an EC2 instance with the correct version of Ubuntu [Ubuntu 16.04 - LTS(HVM)].
- Instance type should be left as t2.micro (1 CPU and 1 GB memory)
    - --> NEXT PAGE
- Leave number of instances as 1.
- Leave the Network as default [vpc-07e47e9d90d2076da (default)]
- Select the Subnet that ends in [DevOpsStudent default 1a | Default in eu-west-1a]
- Auto-assign Public IP must be set as "Enabled"
- All other settings should be left alone, they DO NOT need to be changed
    - --> NEXT PAGE
- The storage allocated to the machine should be left alone.
    - --> NEXT PAGE
- Add a tag with the label "Name" and name it according with the correct naming convention, e.g. `SRE_will_app`
    - --> NEXT PAGE
- The security group name needs to be named under the same naming convention as the machines Name tag. The description can have extra details or copy the security group name.
- Leave the default SSH port 22, this is required.
- Add a rule > SSH with your own private IPV4 address.
- Add a rule > HTTP with 0.0.0.0 IP address, to allow for public access from any IP.
    - --> REVIEW AND LAUNCH
- Choose an existing key, choose the relevant (sre_key.pem) > **Launch the machine**

We can also add the `port 3000` rule now as well, or complete this later.

Navigate to the security groups for the VM and add an inbound rule.
- The port should be `3000`
- `Source` should be set to `Anywhere-IPV4`.

---
#### Access the machine through the terminal
---
- Get the key and save it in a memorable directory.
- Navigate to the directory and change the permissions for the key file to read only > `chmod 400 sre_key.pem`
- Connect to the machine > `ssh -i "sre_key.pem" ubuntu@ec2-3-250-79-225.eu-west-1.compute.amazonaws.com`

---
#### Provision the machine
---
To provision this "app" machine, the process is almost exactly the same as provisioning the locally hosted "app" machine. Here is the `provision.sh` file used to provision the remotely hosted "app" machine:
```
!#/bin/bash

sudo apt-get update -y
sudo apt-get upgrade -y


sudo apt-get install nginx -y

sudo apt-get install npm -y
sudo apt-get install python-software-properties -y
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y

sudo npm install pm2 -g -y
```

---
#### Syncing the app folder
---
**This is compeleted from the localhost terminal**

We need the app folder to be available on the Ubuntu VM. It is already available on our local machine, so all we need to do is copy from our machine, to the remote Ubuntu machine. This is done using the `scp` command:
```
scp -ri ~/.ssh/sre_key.pem ~/Documents/AWS\ Stuff/Move_app_and_db_to_AWS/app ubuntu@3.250.79.225:/home/ubuntu/app
```

The basic syntax for this command is
```
scp [OPTION] [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2
```
We then need to confirm we want to do this.

---
#### Run the app
---
Login to the machine and navigate to the app folder.
Execute the commands:
- `npm install`
- `npm start`

The app will now run, to see it, navigate to the public IPV4 address thought port 3000