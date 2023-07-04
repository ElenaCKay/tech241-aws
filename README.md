# AWS

- EC2 VM -> similar to azure VMs
- S3 buckets -> similar to azure blob storage
- Can use python to manipulate S3 buckets

## Differences between AWS and Azure

- resource groups
  - In Azure, everything must go into a resource group
  - In AWS, they are not necassary 
- IP Address
  - In Azure, by default uses static -> doesnt change
  - In AWS, by default uses dynanmic -> changes everytime your VM restarts
- Terminology
  - Launch on aws is create on azure
  - ubuntu not adminuser
  - terminate not delete

**Do NOT LEAK credentials for AWS. This is in a folder in .ssh. **

## ssh key pair creation

- type key pair into the search bar
- select key pair EC2
- tech241 is our key pair
- it is an RSA type
- pem file as it is ssh
- If creating the key; you would download the pem file which is the private key and it keeps the public one

## Instances in AWS (Creating a VM)

When on the aws console home be in the **Ireland** region. This region is reserved for training. 

- Go into EC2.
- Instances running -> shows how many VMs are running. Also can click on this to go to the VMs and make one.
- Launch instances

![launch instance](imgs/launch-instance.png)

- Give the vm a name
- Choose an img -> OS
  - search for Ubuntu 18.04 LTS 1e9
  - ubuntu/images/hvm-ssd/ubuntu-bionic-18.04-amd64-server-20230424
  - id: ami-0a7493ba2bc35c1e9

![choose img and os](imgs/choose-img-os.png)

- Instance type is t2.micro
- Key pair is tech241
- Has a default virtual network
- Create a new security group - this will be reused 
- set to anywhere 0.0.0.0 -> not the best practice but is okay for training
- edit network settings to allow port 3000 rule
  - name: tech241-elena-app-sg-ssh-http-3000 (sg stands for security group)
  - description: tech241-elena-app-sg-ssh-http-3000 for sparta app
  - add security group rule 
    - port range 3000
    - TCP
    - Source type: Anywhere
    - description: Sparta app

![reused security group](imgs/security-group.png)

- Leave default for configure storage
- Leave advanced details
- Launch instance!
- It gives you an ID so you can go to the correct VM and it is running

## Connecting to VM

- Click connect
- SSH tab
- The privite ssh key is in the .ssh folder
- `chmod 400 tech241.pem` -> Git bash still gave others and group read permissions because it isnt proper linux
- `ssh -i "~/.ssh/tech241.pem" ubuntu@ec2-176-34-86-235.eu-west-1.compute.amazonaws.com`
  - the ip address will change every time so you cannot reuse the command

## Delete VM

- Terminate means delete 
- located in instance state 

## Deploying scripts in AWS

As I am creating two VMs I created a txt file which says I am the app in the home directory of the app VM. I alos created on for the database VM.

In both VMs I created the provison.sh file and by using `nano` and then copied and pasted the relevent scripts from my github repo 'Deploying and Automating'. I then give myself execute permissions using `chmod u+x provision.sh` . I first ran the database script `./provision.sh` . This needed to be run twice to work? I may need a restart for mongodb in there somewhere. Then I ran the app script `./provison.sh`. Then I checked to see if they worked by going to the public app IP address and seeing if the homepage and the posts page came up which they did. 

## Difference between public and privite IP address

Coming into the VM from the outside you can use the publicIP address. To connect the two VMs we have been using the public database IP addess but this changes everytime so it cannot be automated. Using a public ip address is like going outside and entering again and it means you are no longer trusted and you have to pass the NSG rules. 

To overcome this use the private Ip address. When we do this it means the two NIC in the two VMs can communicate. This means the two machines can trust each other and they dont need the rules. So you dont need the 27017 port rule anymore. Use the private Ip address in the environment variable. This will only work if both VMs are in the same virtual networks.

![public vs private ip addresses](imgs/public-vs-private.jpg)

## Adding user data

So that the VM will run the database script straight away, in advanced settings, under user data, paste in the data base script. It will only run *ONCE!* When this is done launch the VM.

The new vm is called tech241-elena-db-user-data. Connect using this command:

  `ssh -i "~/.ssh/tech241.pem" ubuntu@ec2-3-251-71-144.eu-west-1.compute.amazonaws.com`

![user data img](imgs/user-data.png)

When I connected I checked if it worked by checking the status of mongodb and if the bind ip has changed and it worked.

## AMI - Amazon Machine Image

An Amazon Machine Image (AMI) is a template that contains a software configuration (for example, an operating system, an application server, and applications). From an AMI, you launch an instance, which is a copy of the AMI running as a virtual server in the cloud.

### Making a AMI

1. Search EC2
2. Left column has images and under that is AMIs -> This how you find an ami
3. To create on go to instances
4. Pick the instance you want to make an ami of
5. Actions
6. Image and templates
7. Create Image

![Alt text](imgs/ami-creation.png)

8. Give it a name and description and tag
9. Create it

![Alt text](imgs/ami-creation2.png)

When this is done you can launch an instance from it. This means that you dont need an OS as it is already chosen. When you ssh in it says login as ubuntu not root. To stop this change root to ubuntu:

`ssh -i "~/.ssh/tech241.pem" root@ec2-54-170-127-17.eu-west-1.compute.amazo.com`

to 

`ssh -i "~/.ssh/tech241.pem" ubuntu@ec2-54-170-127-17.eu-west-1.compute.amazo.com`