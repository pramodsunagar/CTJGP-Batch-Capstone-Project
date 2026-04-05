## Ansible Tasks:

Once you have created a new instance using Terraform (as part of the Terraform task), SSH into that instance and install Ansible on it. After that, install the **httpd web server and Docker** on the managed node using Ansible playbooks.

Since you do not have separate managed nodes, use your **Ansible workstation itself as the managed node**

#### Steps:
Install ansible using the following commands
```
sudo apt update
```
```
sudo apt install python3 python3-pip wget -y
```
```
sudo pip3 install boto boto3 ansible
```
```
ansible --version
```

Create a inventory in the location **/etc/ansible/hosts** and add the below
```
localhost ansible_connection=local
```
Create a directory
```
mkdir ansible-lab && cd ansible-lab
```
```
vi playbook.yaml
```
Add the given content, by pressing `INSERT`
```
- name: This play will install httpd web servers on all the hosts
  hosts: localhost
  become: yes
  tasks:
    - name: Task1 will install web-server
      apt:
        name: apache2
        update_cache: yes
        state: latest
    - name: Task2 will start the web-server
      service:
        name: apache2
        state: started
```
Execute the playbook
```
ansible-playbook playbook.yaml
```
Access the web server using the public IP of the same machine on **port 80**
```
http://<EC2_PUBLIC_IP>:80
```

#### Docker installation 

Create a playbook named `install-docker.yml`

```bash
vi install-docker.yml
```
Add the given content, by pressing `INSERT`
```yaml
---
- name: Install Docker on all servers
  hosts: localhost
  become: true
  tasks:
  - name: Update apt packages
    apt:
      update_cache: yes

  - name: Install required dependencies
    apt:
      name:
        - apt-transport-https
        - ca-certificates
        - curl
        - software-properties-common
      state: present

  - name: Add Docker GPG key
    apt_key:
      url: https://download.docker.com/linux/ubuntu/gpg
      state: present

  - name: Add Docker repository
    apt_repository:
      repo: deb https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
      state: present

  - name: Update apt again
    apt:
      update_cache: yes

  - name: Install Docker
    apt:
      name: docker-ce
      state: present

  - name: Start and enable Docker service
    service:
      name: docker
      state: started
      enabled: yes
```
Execute the playbook from the Ansible control node.
```bash
ansible-playbook install-docker.yml
```

Run the following command to verify `Docker Installation` on the localhost:
```bash
docker --version
```


