# No2FA
No two-factor Auth requirements

GitHub, let me decide how I want my account secured.  

2FA is NOT "more secure" than a good password.  

It's just another layer of crap to get logged in.  

Gitlab requires a phone number and email to create an account.  


An option is to host your own Gitea server.  Do not explose this configuration to the Internet with these default passwords.  

```
sudo yum install podman
```

```
podman run -d -p 3306:3306 --restart always -v gitea-db-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=gitea -e MYSQL_USER=gitea -e MYSQL_PASSWORD=gitea -e MYSQL_DATABASE=gitea --name gitea-db localhost:5000/mariadb:11.2.2-jammy
```

Open up the port for Mysql on the "internal" zone:  
sudo firewall-cmd --add-service=mysql --zone=internal  
sudo firewall-cmd --add-service=mysql --zone=internal --permanent  
sudo firewall-cmd --reload  

Test the connection to the Database using `mycli`  
```
sudo yum install mycli
mycli mysql://gitea@localhost:3306/gitea
Password: gitea
```

sudo firewall-cmd --add-port=3000/tcp --add-port=2222/tcp --zone=internal  
sudo firewall-cmd --add-port=3000/tcp --add-port=2222/tcp --zone=internal --permanent  
sudo firewall-cmd --reload  

Note: Your IP address of your host needs to be in the env `GITEA__database__HOST=192.168.1.23`  
```
podman run -d -p 3000:3000 -p 2222:2222 --restart always -v gitea-data:/var/lib/gitea -v gitea-config:/etc/gitea -e GITEA__database__DB_TYPE=mysql -e GITEA__database__HOST=192.168.1.23:3306 -e GITEA__database__NAME=gitea -e GITEA__database__USER=gitea -e GITEA__database__PASSWD=gitea --name gitea localhost:5000/gitea:1.21.3-rootless
```

You should be able to open a browser on the same host and browse to:  
`http://localhost:3000/`

Register a new account.  This will be the admin account.  

After the intial start, the containers can be stopped with (stop the app container first):  
```
podman stop gitea
podman stop gitea-db
```

And restarted with (please start the db container first):  
```
podman start gitea-db
podman start gitea
```

You can then use the Migration tools in the GUI to move your repos.

