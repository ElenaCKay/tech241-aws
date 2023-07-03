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

When on the aws console home be in the Ireland region. This region is reserved for training. 

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
