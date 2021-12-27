# Instalación NetBox
1. `sudo apt-get update`
2. `sudo apt-get install -y postgresql libpq-dev`
3. `sudo -u postgres psql`
```
CREATE DATABASE netbox;
CREATE USER netbox WITH PASSWORD ‘PASSWORD’;
GRANT ALL PRIVILEGES ON DATABASE netbox TO netbox;
\q
```
4. `sudo apt-get install -y python3 python3-pip python3-dev build-essential libxml2-dev`
5. `libxslt1-dev libffi-dev libpq-dev libssl-dev redis-server zlib1g-dev`
6. `sudo mkdir -p /opt/netbox/ && cd /opt/netbox/`
7. `sudo apt-get install -y git`
8. `sudo git clone -b master https://github.com/netbox-community/netbox.git .`
9. `sudo su - root`
10. `pip3 install -r /opt/netbox/requirements.txt`
11. `pip3 install gunicorn`
12. `exit`
13. `cd /opt/netbox/netbox/netbox`
14. `sudo cp configuration.example.py configuration.py`
15. `sudo python3 ../generate_secret_key.py`
15.1 *save de password generated!*
16. `sudo nano configuration.py`
```
'NAME': 'databasename',
'USER': 'USERNAME'
'PASSWORD?: 'DATABASE_PASSWORD',
SECRET_KEY = 'PASSWORD_GENERATED_ON_15'
```
17. `cd /opt/netbox/netbox/`
18. `python3 manage.py migrate`
19. `python3 manage.py createsuperuser`
20. `sudo python3 manage.py collectstatic  –no-input`
21. `sudo apt install -y nginx`
22. `sudo nano /etc/nignix/sites-available/netbox`
```
server {
      listen 80;
       server_name ip_maquina;
       client_max_body_size 25m;
        location /static/ {
               alias /opt/netbox/netbox/static/;
        }
        location / {
               proxy_pass http://127.0.0.1:8001;
               proxy_set_header X-Forwaded-Host $server_name;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwaded-Proto $scheme;
               add_header P3P ‘CP=”ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV”’;
         }
}
````
23. `cd /etc/nginx/sites-enabled/`
24. `sudo rm /etc/nginx/sites-enabled/default`
25. `sudo ln -s /etc/nginx/sites-available/netbox`
26. `sudo nano /opt/netbox/gunicorn_config.py`
```
command = ‘/usr/bin/gunicorn’
pythonpath = ‘/opt/netbox/netbox’
bind = ‘127.0.0.1:8001’
workers = 3
user = ‘www-data’
```
27. `sudo apt install -y supervisor`
28. `sudo nano /etc/supervisor/conf.d/netbox.conf`
```[program:netbox]
command = gunicorn -c /opt/netbox/gunicorn_config.py netbox.wsgi
directory = /opt/netbox/netbox/
user = www.data
[program:netbox-rqworker]
command = python3 /opt/netbox/netbox/manage.py rqworker
user = www-data
service nginx restart`
service supervisor restart`
```
## DONE!!
