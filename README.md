# Linux Server Configuration
The second project in Udacity's full stack web development nanodegree program.
The aim of the project to take a baseline installation of a Linux server and prepare it to host your web applications

**Prerequisites :**
- The web application is [Item Catalog](https://github.com/MahaHajaj/Catalog-App)
- The web server is [Amazon lightsail](https://lightsail.aws.amazon.com/)
- The IP address is
- The SSH port is 2200
- The complete URL is

## Steps to Configure Linux server :
- **Get your server :**
 1. Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
     - Log in!
     - Create an instance.
     - Choose an instance image: Ubuntu.
     - Choose your instance plan.
     - Give your instance a hostname.
     - Wait for it to start up.
2. SSH into the server.
- **Secure your server :**
3. Update all currently installed packages.
   ```
   sudo apt-get update
   sudo apt-get upgrade
   ```
4. Change the SSH port from 22 to 2200.
   - Run ```sudo nano /etc/ssh/sshd_config```.
   - Change the port from 22 to 2200.
   - Reload SSH using ```sudo nano /etc/ssh/sshd_config```.
5. Configure the Uncomplicated Firewall (UFW).
   - Configure to allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
   ```
   sudo ufw allow 2200/tcp
   sudo ufw allow 80/tcp
   sudo ufw allow 123/udp
   sudo ufw enable
   ```
- **Give ```grader``` access :**
6. Create a new user account named ```grader```.
   - ```sudo adduser grader```
7. Give ```grader``` the permission to ```sudo```.
   - ```sudo nano /etc/sudoers.d/grader```.
   - Add this line ```grader ALL=(ALL:ALL) ALL```.
   - Save and exit.
8. Create an SSH key pair for ```grader``` using the ```ssh-keygen``` tool.
   - Run ```ssh-keygen``` in the local machine.
   - Give a file name to the key.
   - In the server Run
     ```
     mkdir .ssh
     touch .ssh/authorized_keys
     nano .ssh/authorized_keys
     ```
    - Paste publice key then save and exit.
    - Set the permissions using
    ```
    chmod 700 .ssh
    chmod 644 .ssh/authorized_keys
    ```
    - login using ```ssh grader@[ip-address] -p 2200 -i ~/.ssh/[filename]```
    - To enforce key-based authentication run ```sudo nano /etc/ssh/sshd_config```
    - Change the PasswordAuthentication to no then save and exit.
    - Restart using ```sudo service ssh restart```
- **Prepare to deploy your project :**
9. Configure the local timezone to UTC.
10. Install and configure Apache to serve a Python mod_wsgi application.
11. Install and configure PostgreSQL.
12. Install ```git```.
- **Deploy the Item Catalog project :**
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your ```.git``` directory is not publicly accessible via a browser!
