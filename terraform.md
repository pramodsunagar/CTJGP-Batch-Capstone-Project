
## Step 1: Terraform Workstation Setup

### Objective
Prepare a Terraform workstation to provision cloud infrastructure on AWS.

### Steps
In your terraform WS, install terraform using following commands
```
sudo apt update
```
```
sudo apt install wget unzip -y
```
```
wget https://releases.hashicorp.com/terraform/1.10.3/terraform_1.10.3_linux_amd64.zip
```
```
unzip terraform_1.10.3_linux_amd64.zip
```
```
sudo mv terraform /usr/local/bin
```
```
rm terraform_1.10.3_linux_amd64.zip
```

Install the aws cli
```
sudo apt-get install python3-pip -y
```
```
sudo pip3 install awscli --break-system-packages
```

Use aws configure and give your credentials
```
aws configure
```

Create a directory and inside that directory create your Terraform files to create an instance and ssh into it.
```
mkdir lab
```
```
cd lab
```
```
vi main.tf
```
Add the given content, by pressing `INSERT`

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "tls_private_key" "capstone_key_pair" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "capstone_key" {
  key_name   = "capstone-key"
  public_key = tls_private_key.capstone_key_pair.public_key_openssh
}

resource "local_file" "private_key" {
  content  = tls_private_key.capstone_key_pair.private_key_pem
  filename = "capstone-key"
}

resource "local_file" "public_key" {
  content  = tls_private_key.capstone_key_pair.public_key_openssh
  filename = "capstone-key.pub"
}

resource "aws_security_group" "terraform_sg" {
  name = "sirin-allow-ssh"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "ansible_server" {
  ami                    = "ami-023c11a32b0207432"
  instance_type          = "t2.micro"
  key_name               = aws_key_pair.capstone_key.key_name
  vpc_security_group_ids = [aws_security_group.terraform_sg.id]

  tags = {
    Name = "Ansible-Server"
  }
}

```
Initialise the directory
```
terraform init
```
Plan
```
terraform plan
```
Apply
```
terraform apply -auto-approve
```
Once the resources are created, login into the newly created Instance using the below command
```
ssh -i "capstone-key" ubuntu@IP
```
