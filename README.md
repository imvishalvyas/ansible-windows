# ansible-windows
This repository contains ansible configurations for installing software on Windows systems.

## prerequisite
This command sets the network category of a connection profile to "Private" in Windows.
```
Set-NetConnectionProfile -NetworkCategory Private
```
This command retrieves the network connection profiles on the local computer. It provides information about each network connection, including the network name, interface alias, interface index, and network category (Public, Private, or Domain).
```
Get-NetConnectionProfile
```
This command is used to configure Windows Remote Management (WinRM) quickly. WinRM is a Microsoft implementation of the WS-Management Protocol, which allows remote management of computers running Windows. Running winrm quickconfig enables basic WinRM settings on the local computer, such as starting the WinRM service, setting up the WinRM listener, and allowing WinRM through the Windows Firewall. This command is often used when setting up systems for remote administration or automation tasks.
```
winrm quickconfig
```

Software will be installed on a remote system.
```
git
curl
jq
nodejs
pm2
openjdk17
```

## 1. Copy this script in remote windows system.
You can copy this file in any drive or directory in the windows system.
```
D:\ConfigureRemotingForAnsible.ps1
```

## 2. Execute below commands to run the script.
Open the powershell command prompt and fire the command.

```
 powershell.exe -ExecutionPolicy Bypass -File D:\ConfigureRemotingForAnsible.ps1 -CertValidityDays 100
 ```
 ```
 Set-ExecutionPolicy RemoteSignedc
 ```
You will need to allow the port `59861` from you windows machine to allow remote installation.

## 3. Go to the ansible server.
Go to the directory `/etc/ansible/` and create a installation file `install_software.yml`
```
---
- name: Install Git, Curl, and jq on Windows 10
  hosts: windows
  gather_facts: no
  tasks:
    - name: Install Git using Chocolatey
      win_chocolatey:
        name: git
        state: present
      vars:
        ansible_winrm_server_cert_validation: ignore

    - name: Install Curl using Chocolatey
      win_chocolatey:
        name: curl
        state: present
      vars:
        ansible_winrm_server_cert_validation: ignore
          
    - name: Install jq using Chocolatey
      win_chocolatey:
        name: jq
        state: present
      vars:
        ansible_winrm_server_cert_validation: ignore
          
    - name: Install Node.js using Chocolatey
      win_chocolatey:
        name: nodejs
        state: present
        version: 18.17.0
      vars:
        ansible_winrm_server_cert_validation: ignore          

    - name: Install PM2 using npm
      win_shell: npm install -g pm2        
      vars:
        ansible_winrm_server_cert_validation: ignore

    - name: Install JDK 17 using Chocolatey
      win_chocolatey:
        name: openjdk17
        state: present
      vars:
        ansible_winrm_server_cert_validation: ignore
```

## 4. Update client host
Open the host file in ansible directory and update client details.
```
[windows]
192.168.1.20 ansible_user=Admin ansible_password=Your_password ansible_connection=winrm
```

## 5. Execute the installtion
Run below command to execute commands written in the ansible.
```
ansible-playbook -i hosts install_software.yml
```
It will take a few minutes to install the required software.
