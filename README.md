# Linux Server Configuration
The second project in Udacity's full stack web development nanodegree program.
The aim of the project to take a baseline installation of a Linux server and prepare it to host your web applications

**Project Discription :**
- The web application is [Item Catalog](https://github.com/MahaHajaj/Catalog-App)
- The web server is [Amazon lightsail](https://lightsail.aws.amazon.com/)
- The IP address is 18.196.237.43
- The SSH port is 2200
- The complete URL is http://ec2-18-196-237-43.eu-central-1.compute.amazonaws.com

## Steps to Configure Linux server :
- **Get your server**
1. Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
   - Log in!
   - Create an instance.
   - Choose an instance image: Ubuntu.
   - Choose your instance plan.
   - Give your instance a hostname.
   - Wait for it to start up.
2. SSH into the server.
- **Secure your server**
3. Update all currently installed packages.
   ```
   sudo apt-get update
   sudo apt-get upgrade
   ```
4. Change the SSH port from 22 to 2200.
   - Run ```sudo nano /etc/ssh/sshd_config```.
   - Change the port from 22 to 2200.
   - Reload SSH using ```sudo service ssh restart```.
5. Configure the Uncomplicated Firewall (UFW).
   - Configure to allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
     ```
     sudo ufw allow 2200/tcp
     sudo ufw allow 80/tcp
     sudo ufw allow 123/udp
     sudo ufw enable
     ```
    - Make sure to deny port 22 after login using ```ssh -i lightsail_key.rsa grader@18.196.237.43 -p 2200 ```
- **Give ```grader``` access**
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
    - login using ```ssh grader@18.196.237.43 -p 2200 -i ~/.ssh/LinuxServer```
    - To enforce key-based authentication run ```sudo nano /etc/ssh/sshd_config```
    - Change the PasswordAuthentication to no then save and exit.
    - Restart using ```sudo service ssh restart```
- **Prepare to deploy your project**
9. Configure the local timezone to UTC using ```sudo dpkg-reconfigure tzdata```.
10. Install and configure Apache to serve a Python mod_wsgi application.
    - Run ```sudo apt-get install apache2```
    - Run ```sudo apt-get install libapache2-mod-wsgi```
    - Start the server ```sudo service apache2 start```
    - Run ```sudo nano /etc/apache2/sites-available/catalog.conf```
    - Paste the following code
    ```
    <VirtualHost *:80>
    ServerName 18.196.237.43
    ServerAdmin admin@18.196.237.43
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```
    - Run ```sudo a2ensite catalog```  
11. Install and configure PostgreSQL.
    - Login using ```sudo su - postgres```
    - Run these command
    ```
    CREATE USER catalog WITH PASSWORD 'password';
    ALTER USER catalog CREATEDB;
    CREATE DATABASE catalog WITH OWNER catalog;
    \c catalog
    REVOKE ALL ON SCHEMA public FROM public;
    GRANT ALL ON SCHEMA public TO catalog;
    \q
    exit
    ```
12. Install ```git```.
- **Deploy the Item Catalog project**
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
    - Run ```cd /var/www``` then ```sudo mkdir catalog```
    - Run ```sudo chown -R grader:grader catalog```
    - Run ```sudo chmod catalog``` to chang permission
    - Run ```cd catalog``` then ```git clone https://github.com/MahaHajaj/Catalog-App.git```
          - Change ```engine = create_engine('sqlite:///catalogdb.db')``` to ```engine =       create_engine('postgresql://catalog:password@localhost/catalog')```
          - And Change ```'client_secrets.json'```to ```'/var/www/catalog/client_secrets.json'```
    - Chang the branch using ```git checkout configrationServer ```
    - Create ```/var/www/catalog/catalog.wsgi``` file
    - Run ```nano /var/www/catalog/catalog.wsgi```
    - Add these line
      ```
      import sys
      import logging
      logging.basicConfig(stream=sys.stderr)
      sys.path.insert(0, "/var/www/catalog/catalog/")
      sys.path.insert(1, "/var/www/catalog/")

      from catalog import app as application
      application.secret_key = 'super_secret_key'
       ```
    - Restart apache using ```sudo service apache2 restart```
14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your ```.git``` directory is not publicly accessible via a browser!
- **Resources**
    - Udacity's FSND Knowlege and Stackoverflow
    - http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/
    - https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
    - [boisalai](https://github.com/boisalai/udacity-linux-server-configuration) README.md file
