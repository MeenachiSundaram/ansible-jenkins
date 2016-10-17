# ansible-jenkins playbook
Installing Jenkins in Ubuntu

## Installing Ansible

- `sudo yum update`

- `sudo yum install ansible`

## Writing first playbook

create `configure.yml`

  ```
  ---
    - name: Configure
      hosts: jenkins-packer
      become: yes
      become_method: sudo
      gather_facts: no
      roles:
          - git
          - java
          - jenkins
  ```

#### Creating roles

- Create the folder `roles/git/tasks` and add a file named `main.yml` inside of it.

    ```
    ---
      - name: installing git & tree
        apt:
          name=git state=present
          name=tree state=present
    ```

- Create the folder `roles/java/tasks` and add a file named `main.yml` inside of it.
    ```
    ---
      - name: Updating cache and installing openjdk-7-jre&jdk
        apt:
          update_cache=yes
          name=openjdk-7-jre state=present
          name=openjdk-7-jdk state=present
    ```

- Create the folder `roles/jenkins/tasks` and add a file named `main.yml` inside of it.
    ```
    ---
      - name: Installing wget
        apt:
          name=wget state=present

      - name: Adding Jenkins APT-Key
        apt_key: url=https://pkg.jenkins.io/debian/jenkins-ci.org.key state=present

      - name: Creating jenkins.list
        shell: sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
        args:
          creates: /etc/apt/sources.list.d/jenkins.list

      - name: Installing jenkins
        apt:
          name=jenkins state=present
    ```  

## Creating inventory

Create an inventory file named it hosts, in the same directory as our playbook (configure.yml) with the following content:

  ```
  [jenkins-packer]
  jenkins-packer ansible_ssh_host=<host ip> ansible_ssh_port=22 ansible_ssh_user=<host username> ansible_ssh_private_key_file=<location to pem key file>
  ```

## First Launch

Now run `ansible-playbook configure.yml -i hosts`
