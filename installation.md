
# Configuration du Serveur Web Golang

## Prérequis

- 4 noms de domaine valides dont :
    - 1 pour le serveur golang (ex: `artistesynovram.online`)
    - 2 pour des pages html (ex: `simplepagehtml.artistesynovram.online`, `uneautrepagehtml.artistesynovram.online`)
    - 1 pour le monitoring (ex: `monitoring.artistesynovram.online`)
- 1 machine, avec 2 Go de RAM, 1 CPU et 40 Go Disk, sous Debian 11

## Connexion au serveur

ssh user@145.239.198.232  
mot de passe fourni par l'hébergeur

## Serveur Golang

### Clone du Dépôt Git

sudo apt update  
mkdir /var/www  
cd /var/www  
sudo apt install git -y  
git clone "https://git.ytrack.learn.ynov.com/MDOUBLAIT/groupie-tracker.git"

IDENTIFIANT GIT  
MDP GIT


### Installation de Nginx

sudo apt install nginx -y  
sudo systemctl start nginx  
sudo systemctl enable nginx

### Configuration de Nginx

sudo chmod +x -R groupie-tracker/  
sudo chown -R www-data:www-data groupie-tracker/

sudo nano /etc/nginx/sites-available/artistesynovram.online

``` bash
server {
        listen 80;
        listen [::]:80;                       

    server_name artistesynovram.online; 

    location / {            
        try_files $uri $uri/ =404;
        proxy_pass http://localhost:5500;
    }
}
```

sudo ln -s /etc/nginx/sites-available/artistesynovram.online /etc/nginx/sites-enabledcartistesynovram.online

sudo systemctl restart nginx

### Installation de Golang

cd /var/www/groupie-tracker/  
sudo apt install golang -y  
sudo go build -o run-groupie  

### Configuration du Serveur Golang

nano /etc/systemd/system/go-web.service

``` bash
[Unit]
Description=golang web server

[Service]
Type=simple
Restart=always
RestartSec=5s
User=www-data
Group=www-data
WorkingDirectory=/var/www/groupie-tracker
ExecStart=/var/www/groupie-tracker/run-groupie

[Install]
WantedBy=multi-user.target
```

sudo systemctl start go-web.service


## Pages Web

### Création des pages Web
sudo nano /var/www/html/simplepagehtml.html

```bash
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>Titre</title>
    </head>

    <body>
            <p>simplepagehtml</p>
    </body>
</html>
```
sudo nano /var/www/html/uneautrepagehtml.html

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>Titre</title>
    </head>

    <body>
            <p>uneautrepagehtml</p>
    </body>
</html>
```

### Configuration Nginx

sudo nano /etc/nginx/sites-available/simplepagehtml.artistesynovram.online

```
server {
        listen 80;
        listen [::]:80;    

    root /var/www/html;    

    index simplepagehtml.html;                        

    server_name simplepagehtml.artistesynovram.online; 

    location / {            
        try_files $uri $uri/ =404;
    }
}
```

sudo ln -s /etc/nginx/sites-available/simplepagehtml.artistesynovram.online /etc/nginx/sites-enabled/simplepagehtml.artistesynovram.online

sudo nano /etc/nginx/sites-available/uneautrepagehtml.artistesynovram.online

```
server {
        listen 80;
        listen [::]:80;    

       root /var/www/html;    

    index uneautrepagehtml.html;                        

    server_name uneautrepagehtml.artistesynovram.online; 

    location / {            
        try_files $uri $uri/ =404;
    }
}
```

sudo ln -s /etc/nginx/sites-available/uneautrepagehtml.artistesynovram.online /etc/nginx/sites-enabled/uneautrepagehtml.artistesynovram.online

## Sécurisation

### Mise en place du HTTPS (certbot)

sudo apt install certbot  -y

web site url: artistesynovram.online en http

sudo apt install snapd -y  
sudo snap install core; sudo snap refresh core  
sudo apt-get remove certbot  
sudo snap install --classic certbot  
sudo ln -s /snap/bin/certbot /usr/bin/certbot  
sudo certbot --nginx

sudo certbot renew --dry-run



### Installation du pare-feu (ufw)  

sudo apt-get update && sudo apt-get install ufw -y   
sudo ufw allow ssh  
sudo ufw allow http  
sudo ufw allow https  
sudo ufw allow 22  
sudo ufw allow 80  
sudo ufw allow 8080  
sudo ufw allow 2222   

sudo ufw allow 19999 (net data) 

sudo ufw enable -y   
sudo ufw reload -y

EXTRA :   
sudo ufw status numbered  
sudo ufw status verbose  

Supprimer la règle 3 : sudo ufw delete 3  
Bloquer une IP : sudo ufw deny 192.168.50.22

sudo ufw reload


### Empêcher la Brute-force (fail2ban):

sudo apt-get install fail2ban

cp /etc/fail2ban/jail.conf     /etc/fail2ban/jail.local

Conf fail2ban : sudo nano /etc/fail2ban/jail.local 


### Mise en place du monitoring (netdata)

sudo apt-get update && sudo apt-get upgrade -y  
sudo apt-get install netdata -y  
sudo systemctl start netdata  
sudo systemctl enable netdata  
sudo systemctl status netdata 

nano /etc/netdata/netdata.conf  

```bash
bind socket to IP = 127.0.0.1  
```

sudo systemctl restart netdata  





