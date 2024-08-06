# dynamic-web-app-on-docker

The repository to store dockerfile

Aosnote Project 5

After installing docker and VS code, the next thing is to create VPC
VPC using the cidr 10.0.0.0/16, enable dns hostname in the vpc
Create internet gateway and attach it to the vpc
Create 6 subnets using two AZ
Public Subnet AZ1 10.0.0.0/24 enable auto-assign ipv4
Public Subnet AZ2 10.0.1.0/24 enable auto-assign ipv4
Private App Subnet AZ1 10.0.2.0/24
Private App Subnet AZ2 10.0.3.0/24
Private Data Subnet AZ2 10.0.4.0/24
Private Data Subnet AZ2 10.0.5.0/24
Create public rt, nat gateway and private rt
Create SG
This project will use 4 SG which are
ALB SG to open HTTP and HTTPS ports from anywhere
Bastion Host SG open ssh port to allow traffic from my_ip address
Container SG open to HTTP and HTTPS from ALB-SG
Data SG open to mysql/aurora and the source is container-sg, also add custom tcp port 3306 and the source should be container-sg
Create an RDS instance, the first thing is to create a subnet group in the RDS Service to choose the subnets and AZ you want your databases to be created.
Create mysql database, for the template select dev/test, under availability choose single db instance but you can choose the multi-az if you want to create a standby db. Username: admin and password: Oluwatobi16, enter database name in additional configuration
Create a GitHub repo to store the application code
Clone the repo on your pc using the ssh url to clone it
Open the folder of the application on VS Code
Create a repo on GitHub to store the dockerfile
Clone the repo on the pc and open on VS code
Create a personal access token on GitHub that docker will use to clone the repository
Token: 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
Locate shell script directory in the terminal and run: chmod +x build_image.sh
If the previous procedure did not work
Build the Dockerfile using docker build -t rentzone .
Creat a repository in Amazon ECR with aws cli
aws ecr create-repository --repository-name <repository-name> --region <region>
aws ecr create-repository --repository-name rentzone --region eu-west-2
Next step is to push the docker image that was built into the Amazon ECR service. Using

# retag docker image

docker tag <image-tag> <repository-uri>
docker tag rentzone 891377225342.dkr.ecr.eu-west-2.amazonaws.com/rentzone

# login to ecr

aws ecr get-login-password | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
aws ecr get-login-password | docker login --username AWS --password-stdin 891377225342.dkr.ecr.eu-west-2.amazonaws.com

# push docker image to ecr repository

docker push <repository-uri>
docker push 891377225342.dkr.ecr.eu-west-2.amazonaws.com/rentzone

create key-pair to ssh into ecr repository
locate the directory where the key is located from the terminal
launch bastions host ec2-instance in order to ssh into RDS instance in the private subnet so that we can migrate the sql into the RDS DB
copy the sql script and paste it in the sql directory in the flyway folder

ssh -i -L LOCAL_PORT:RDS_ENDPOINT:REMOTE_PORT EC2_USER@EC2_HOST -N -f

ssh -i aosnote-kp.pem -L 3306:database-1.cfqakc448l1x.eu-west-2.rds.amazonaws.com:3306 EC2_USER@3.10.232.43 -N -f

ssh -i aosnote-kp.pem ec2-user@3.10.232.43 -L 3307:database-1.cfqakc448l1x.eu-west-2.rds.amazonaws:3306 -N
