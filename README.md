# 🚀 Automated LEMP Stack Deployment using Ansible on AWS

## 📘 Description

This project demonstrates the automated deployment of a LEMP stack 
— Linux, Nginx, MySQL, and PHP
— on an AWS EC2 Ubuntu 24.04 instance using Ansible.
This setup automates the provisioning and configuration of the web server, database, and PHP processor, and verifies functionality via a PHP info page.

---

## 🧰 Technologies Used

| Component   | Tool/Service      |
|------------|--------------------|
| OS         | Ubuntu 24.04 (EC2) |
| Web Server | Nginx              |
| Database   | MySQL              |
| Backend    | PHP                |
| Automation | Ansible            |
| Cloud      | AWS EC2            |

---

## 📁 Project Structure


automated-lemp-setup/
├── inventory.ini
├── lemp_playbook.yml
├── README.md
└── screenshots/
├── ec2-launch.png
├── ssh-access.png
├── inventory-setup.png
├── playbook-run.png
├─phpinfo-confirmation.png


---

## 🔧 Ansible Playbook (`lemp_playbook.yml`)

```yaml

---

- name: Automated LEMP Stack Deployment on AWS
  hosts: webserver
  become: true

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Install MySQL server
      apt:
        name: mysql-server
        state: present

    - name: Install PHP and extensions
      apt:
        name:
          - php-fpm
          - php-mysql
        state: present

    - name: Configure Nginx to use PHP
      copy:
        dest: /etc/nginx/sites-available/default
        content: |
          server {
              listen 80 default_server;
              listen [::]:80 default_server;

              root /var/www/html;
              index index.php index.html index.htm;

              server_name _;

              location / {
                  try_files $uri $uri/ =404;
              }

              location ~ \.php$ {
                  include snippets/fastcgi-php.conf;
                  fastcgi_pass unix:/run/php/php8.3-fpm.sock;
              }

              location ~ /\.ht {
                  deny all;
              }
          }
      notify: Restart Nginx

    - name: Upload PHP info file
      copy:
        src: phpinfo.php
        dest: /var/www/html/phpinfo.php
        mode: '0644'

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
```

## 🧪 Testing the Setup

Open your browser and visit: http://<your-ec2-public-ip>/phpinfo.php

If PHP is configured correctly, you will see the PHP Info page.

---

## 🧩 Troubleshooting Scenarios


Issue	                               Solution

1. Nginx failed to reload	Ensure Apache is stopped: sudo systemctl disable apache2 && sudo systemctl stop apache2

2. MySQL failed to start	Reboot instance and re-run Ansible, ensure no conflicting DB processes

3. PHP info not displaying	Check for correct PHP version and restart Nginx and PHP-FPM

4. 502 Bad Gateway error	PHP-FPM may not be running or wrong socket path

5. Public IP changes after reboot	Use an Elastic IP to assign a permanent public IP to EC2


## 📝 Notes


* Always update and upgrade your instance before running Ansible playbooks.

* Ensure Security Group allows ports 22 (SSH) and 80 (HTTP).

* After deploying, navigate to the public IP or domain (if mapped) to confirm functionality.

* Remove phpinfo.php after testing, as it exposes server configuration.



## ✅ Status


Project Name: Automated LEMP Stack Deployment using Ansible on AWS

Status: ✅ Completed

Type: Hybrid DevOps & Automation

Maintainer: Alan Varghese

