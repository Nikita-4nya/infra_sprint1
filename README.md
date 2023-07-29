# Kittygram



## Запуск проекта

1. Клонируйте репозиторий

```bash
git clone 
```

2. Создайте и активируйте виртуальное окружение в директории ```infra_sprint1/backend/```. Последовательно выполните команды:

```bash
$ cd infra_sprint1/backend/
$ python -m venv venv
$ source venv/bin/activate 
```
3. Установите зависимости. 

```bash
(venv) $ pip install -r requirements.txt 
```
4. В директории ```infra_sprint1/backend/``` создайте файл ```.env```.

```bash
(venv) $ touch .env
```
Создайте секретный ключ:

```bash
(venv) $ python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```
Скопируйте полученный ключ в файл ```.env```:

```bash
SECRET_KEY=<Cекретный ключ>
```
Добавьте в файл ```.env``` следующие строки:

```bash
DEBUG=False

ALLOWED_HOSTS=<IP вашего сервера> 127.0.0.1 localhost
```
5. Выполните миграции и создайте суперюзера:

```bash
(venv) $ python manage.py migrate
(venv) $ python manage.py createsuperuser
```
6. Установите Gunicorn и создайте юнит:

```bash
(venv) $ pip install gunicorn==20.1.0
(venv) $ sudo nano /etc/systemd/system/gunicorn_kittygram.service
```
Запустите юнит:

```bash
sudo systemctl start gunicorn_kittygram
```
7. Установите Nginx:

```bash
sudo apt install nginx -y 
```
Последовательно выполните команды:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable
```
8. Настройте конфигурацию Nginx. 

```bash
sudo nano /etc/nginx/sites-enabled/default
```
```bash
server {
    server_name <>;
    
    location /media {
        autoindex on;
        alias /var/www/kittygram/media/;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /redoc/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location / {
        root   /var/www/kittygram;
        index  index.html index.htm;
        try_files $uri /index.html;
    }
}
```

Перезагрузите Nginx:

```bash
sudo systemctl reload nginx
```
9. В директории ```infra_sprint1/frontend/``` выполните команду:

```bash
npm run build
```

Скопируйте созданную папку в ```/var/www```:

```bash
sudo cp -r /infra_sprint1/frontend/build/. /var/www/kittygram/
```
10. Cоздайте директорию media в директории ```/var/www/kittygram/```
