# PyEditorial
A free, open-source Blog CMS based on the "Django" and "Editorial" HTML5 theme.

![](https://img.shields.io/github/stars/mavenium/PyEditorial) 
[![](https://img.shields.io/github/forks/mavenium/PyEditorial)](https://github.com/mavenium/PyEditorial/fork)
[![](https://img.shields.io/github/issues/mavenium/PyEditorial)](https://github.com/mavenium/PyEditorial/issues)
![](https://img.shields.io/twitter/url?url=https%3A%2F%2Fgithub.com%2Fmavenium%2FPyEditorial)

------------
### Features

- "Blog" section to create and edit a blog + Blog Category
- "Videocast" section to create and edit a videocast + Videocast Category
- "Podcast" section to create and edit a podcast + podcast Category
- "Skill" section to create and edit a skill
- "CONSTANCE" Section to manage dynamic Django settings (Blog title, Social Networks links and ...)
- Displays the list of Blog posts as paged in archive
- Displays the list of Videocast as paged in archive
- Displays the list of podcast as paged in archive
- Used "Django Admin" to manage all models
- Used "Editorial" theme by HTML5 UP
- Used "Sqlite" to create DB
- Used "CKEditor"
- Translation ready
- Auth system (login & logout and forget a password)
- Front-end forms to create new object
------------
[![](https://i.ibb.co/mtmbfhp/buy-me-a-coffee.png)](https://www.blockchain.com/btc/payment_request?address=1ChqZPGhxpn6HB1WuQh55S3Mf8RydxMiFk&amount=0.00018711 "Buy me a coffee")
- You can buy me a coffee so I can turn it into more open source projects :)
------------
### Special Thanks

| Python | Django | Pycharm |
| ------------- | ------------- | ------------- |
| [![](https://s17.picofile.com/file/8418101118/python.png)](https://www.python.org "Python")  | [![](https://i.ibb.co/KbJPgRr/django.png)](https://www.djangoproject.com "Django")  | [![](https://s17.picofile.com/file/8418101034/pycharm.png)](https://www.jetbrains.com/pycharm/ "Pycharm")  |

------------
### Screenshots

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Index.png)
> Index Page

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Archive.png)
> Archive Page

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Blog-Single.png)
> Blog Single Page

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Podcast-Single.png)
> Podcast Single Page

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Videocast-Single.png)
> Videocast Single Page

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Admin.png)
> Admin Area

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Constance.png)
> Dynamic Django Settings

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Blog-Admin.png)
> Blog Section

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Add-Blog.png)
> Add Blog

![](https://raw.githubusercontent.com/mavenium/PyEditorial/master/Screenshots/Add-Videocast.png)
> Add Videocast

![](https://github.com/mavenium/PyEditorial/blob/master/Screenshots/Add-Skill.png)

To deploy your Django project from GitHub using Nginx, MySQL, and an SSL certificate on an Ubuntu server, follow these steps:

### Prerequisites
- An Ubuntu server with root or sudo access.
- A domain name pointing to your server's IP address.
- A GitHub repository containing your Django project.
- A virtual environment set up for your project.
  
### 1. **Update and Install Dependencies**
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install python3-pip python3-dev libpq-dev nginx curl git
   sudo apt install mysql-server libmysqlclient-dev
   ```
Once all the packages are installed, you can start the Apache service and configure it to run on startup by entering the following commands:

```bash
 systemctl start Nginx
 systemctl enable Nginx

```
Now Allow the firewall
```bash
ufw allow 80
ufw allow 443

```

### 2. **Clone Your GitHub Repository**
   ```bash
   cd /var/www/html/
   sudo git clone https://github.com/unfoldadmin/django-unfold.git
   cd django-unfold
   ```

### 3. **Set Up Virtual Environment**
   ```bash
   sudo apt install python3-venv
   python3 -m venv projectenv
   source projectenv/bin/activate
   pip install django
   pip install mysqlclient
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

### 4. **Configure Django Settings**
- Update your `settings.py` to include your domain in the `ALLOWED_HOSTS`:
   ```python
   ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
   ```
- Ensure your `DATABASES` setting points to your MySQL database.

### 5. **Set Up MySQL Database**
   ```bash
   sudo apt install mysql-server
   sudo mysql_secure_installation
   ```
   - Log in to MySQL and create a database and user:
     ```bash
     sudo mysql -u root -p
     ```
     ```sql
     CREATE DATABASE django_unfold;
     CREATE USER 'youruser'@'localhost' IDENTIFIED BY 'yourpassword';
     GRANT ALL PRIVILEGES ON django_unfold.* TO 'youruser'@'localhost';
     FLUSH PRIVILEGES;
     EXIT;
     ```
   - Update your Django `settings.py` to connect to the MySQL database.

  ```bash
  DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_db_name',
        'USER': 'your_db_user',
        'PASSWORD': 'your_db_password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}

 ```

### 6. **Run Migrations and Collect Static Files**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   python manage.py collectstatic
   ```

### 7. **Set Up Gunicorn**
   ```bash
   pip install gunicorn
   gunicorn --bind 0.0.0.0:8000 djangoproject.wsgi
   ```
Next, you need to create a systemd service file to manage the Django. First, create a Gunicorn socket file.
 ```bash
    nano /etc/systemd/system/gunicorn.socket
```
Add the following configuration.
  ```bash 
        [Unit]
        Description=gunicorn socket
        [Socket]
        ListenStream=/run/gunicorn.sock
        [Install]
        WantedBy=sockets.target
   ```
   - Create a system service file for Gunicorn:
     ```bash
     sudo nano /etc/systemd/system/gunicorn.service
     ```
     - Add the following:
       ```bash
       [Unit]
        Description=gunicorn daemon
        Requires=gunicorn.socket
        After=network.target
        [Service]
        User=root
        Group=www-data
        WorkingDirectory=/root/project
                ExecStart=/root/project/djangoenv/bin/gunicorn --access-logfile - --workers 3 --bind unix:/run/gunicorn.sock djangoproject.wsgi:application
            [Install]
        WantedBy=multi-user.target
       ```
     - Enable and start the Gunicorn service:
       ```bash
       sudo systemctl start gunicorn
       sudo systemctl enable gunicorn
       systemctl start gunicorn.socket
       systemctl enable gunicorn.socket
       
       ```

### 8. **Configure Nginx**
   - Create an Nginx server block:
     ```bash
     sudo nano /etc/nginx/sites-available/django_unfold
     ```
     - Add the following:
       ```nginx
       server {
           listen 80;
           server_name yourdomain.com www.yourdomain.com;

           location = /favicon.ico { access_log off; log_not_found off; }
           location /static/ {
               root /var/www/html/django-unfold;
           }

           location / {
               include proxy_params;
               proxy_pass http://unix:/var/www/html/django-unfold/gunicorn.sock;
           }
       }
       ```
   - Enable the configuration and restart Nginx:
     ```bash
     sudo ln -s /etc/nginx/sites-available/django_unfold /etc/nginx/sites-enabled
     sudo nginx -t
     sudo systemctl restart nginx
     ```

Ensure Correct Permissions for Static and Media Files
If you have static and media directories outside of the main project directory, set their ownership and permissions similarly:

```bash
sudo chown -R www-data:www-data /var/www/html/django-unfold/
sudo chmod -R 755 /var/www/html/django-unfold/

```

### 9. **Obtain an SSL Certificate**
   - Install Certbot:
     ```bash
     sudo apt install certbot python3-certbot-nginx
     ```
   - Obtain and install the SSL certificate:
     ```bash
     sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
     ```
   - Follow the prompts to complete the SSL installation. Certbot will automatically configure your Nginx server block to use SSL.

### 10. **Testing**
   - Visit `https://yourdomain.com` to ensure everything is working correctly.

### 11. **Set Up Automatic Renewals for SSL**
   ```bash
   sudo systemctl status certbot.timer
   ```

This setup will get your Django project deployed with Nginx, MySQL, and an SSL certificate on Ubuntu.
