### Notes to Point a domain and to host Node Express Project on Apache Web Server on Hostinger

- Open Command Prompt
- Copy your project folder (delete node modules) from Local Windows Machine to Linux Remote Server
```sh
Syntax:- scp -P Port_number Source_File_Path Destination_Path
Example:- scp -P 22 node_project user@11.19.41.20:
```
- Get Access to Remote Server via SSH
```sh
Syntax:- ssh -p PORT USERNAME@HOSTIP
Example:- ssh -p 22 user@11.19.41.20
```
#### Note:- Run Below Commands on Your Remote Server to verify existing installations

```sh
apache2 -v
node -v
npm -v
pm2 --version
```
- Install Apache
```sh
sudo apt install apache2
```
- Install PM2
```sh
sudo npm install -g pm2@latest
```
- Add PM2 Process on Startup
```sh
sudo pm2 startup
```
- Verify Apache2 is Active and Running
```sh
sudo service apache2 status
```
- Verify Web Server Ports are Open and Allowed through Firewall
```sh
sudo ufw status verbose
```
- Run ls command to verify that the zip file is present
```sh
ls
```

- Move Project Folder to Web Server Public Directory
```sh
Syntax:- sudo mv project_folder_name Destination_Path
Example:- sudo mv node_project /var/www
```
- Go to Your Project Directory
```sh
Syntax:- cd /var/www/project_folder_name
Example:- cd /var/www/node_project
```
- Install Dependencies
```sh
npm install
```

- Create Virtual Host File
```sh
sudo nano /etc/apache2/sites-available/your_domain.conf
```
- Add Following Code in Virtual Host File
- Change the IP and Port According to your Node Application Code
```sh
<VirtualHost *:80>
        ServerName www.example.com
        ServerAdmin contact@example.com
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:8000/
        ProxyPassReverse / http://127.0.0.1:8000/
        <Directory "/var/www/miniblog">
                AllowOverride All
        </Directory>
</VirtualHost>
```
- Check Configuration is correct or not
```sh
sudo apache2ctl configtest
```
- Enable the Proxy module with Apache
```sh
sudo a2enmod proxy
sudo a2enmod proxy_http
```
- Enable Virtual Host
```sh
cd /etc/apache2/sites-available/
sudo a2ensite your_domain.conf
```
- Check if any other process is using port 80. In this case, lightspeed would be running and we need to stop it. If there are any processes, kill them by their PID.
```sh
sudo lsof -i :80
sudo systemctl stop lshttpd.service
sudo systemctl disable lshttpd.service
sudo kill <process_id>
```

- Apache2
```sh
sudo service apache2 restart
OR
sudo systemctl restart apache2
```
- Start Node Express Application using pm2
```sh
cd /var/www/node_project
sudo pm2 start server.js
```
- Save PM2 Process
```sh
sudo pm2 save
```
- Check PM2 Status
```sh
sudo pm2 status
```
- In case of an error, check error
```sh
cd /var/log
su
cd apache2
cat error.log
```
- You can Clear Error Logs (Optional)
```sh
sudo bash -c 'echo > /var/log/apache2/error.log'
```
