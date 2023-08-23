# Социальная сеть kittygram

![workflow](https://github.com/pavlovbjj/kittygram_final/actions/workflows/main.yml/badge.svg)

# Описание проекта Kittygram
Cоциальная сеть для обмена фотографиями любимых питомцев. Можно зарегистрироваться и авторизоваться, добавить нового котика на сайт или изменить существующего, а также просмотреть записи других пользователей.

# Инструкция по разворачиванию

<br>1. Клонировать проекта с GitHub
```
    git clone git@github.com:pavlovbjj/kittygram_final.git
```

<br>2.  Собрать Docker-образы проекта

    ```bash
    Заменить username на ваш логин на DockerHub:

    cd frontend
    docker build -t username/kittygram_frontend .
    cd ../backend
    docker build -t username/kittygram_backend .
    cd ../nginx
    docker build -t username/kittygram_gateway . 
    ```

<br>3. Загрузить образы на DockerHub:

    ```bash
    docker push username/kittygram_frontend
    docker push username/kittygram_backend
    docker push username/kittygram_gateway
    ```

<br>4. Подключиться к удаленному серверу

    ```bash
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
    ```

<br>5. Создать на сервере директорию kittygram через терминал

    ```bash
    mkdir kittygram
    ```

<br>6. Установить docker compose на сервер:

    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

<br>7. В директорию kittygram/ скопировать файлы docker-compose.production.yml и .env:

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
    * ath_to_SSH — путь к файлу с SSH-ключом;
    * SSH_name — имя файла с SSH-ключом (без расширения);
    * username — ваше имя пользователя на сервере;
    * server_ip — IP вашего сервера.
    ```

<br>8. Запустить docker compose в режиме демона:

    ```bash
    sudo docker compose -f docker-compose.production.yml up -d
    ```

<br>9. Выполнить миграции, соберать статические файлы бэкенда и скопировать их в /backend_static/static/:

    ```bash
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

<br>10. На сервере в редакторе nano открыть конфиг Nginx:

    ```bash
    sudo nano /etc/nginx/sites-enabled/default
    ```

<br>11. Изменить настройки location в секции server:

    ```bash
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

<br>12. Проверить работоспособность конфига Nginx:

    ```bash
    sudo nginx -t
    ```
    Если ответ в терминале такой, значит, ошибок нет:
    ```bash
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    ```

<br>13. Перезапустить Nginx
    ```bash
    sudo service nginx reload
    ```

# Описание переменных окружения
```
    В файле .env, который находится в директории /kittygram_final/ должно быть создано семь переменных 
    
        POSTGRES_USER
        POSTGRES_PASSWORD
        POSTGRES_DB
        SECRET_KEY
        DB_HOST
        DB_PORT
        DEBUG
        ALLOWED_HOSTS

    Для переменной POSTGRES_USER необходимо задать имя пользователя для базы
    данных PostgreSQL. Пример: POSTGRES_USER=kittygram_user

    Для переменной POSTGRES_PASSWORD необходимо задать пароль для базы
    данных PostgreSQL. Пример: POSTGRES_PASSWORD=kittygram_password

    Для переменной POSTGRES_DB необходимо задать название базы
    данных PostgreSQL. Пример: POSTGRES_DB=kittygram

    Для переменной DB_HOST необходимо задать адрес, по которому Django будет
    соединяться с базойданных PostgreSQL. Пример: DB_HOST=db

    Для переменной DB_PORT необходимо задать порт, по которому Django будет
    соединяться с базой данных PostgreSQL. DB_PORT=5432

    В переменную SECRET_KEY необходимо ввести ваш секретный код в виде:
    SECRET_KEY = '<ваш_ключ>'

    В переменную DEBUG нужно присвоить False: DEBUG = False

    В переменную ALLOWED_HOSTS добавить локальные адреса, а также
    доменное имя или внешний IP (если есть):

    ALLOWED_HOSTS = xxx.xxx.xxx.xxx,127.0.0.1,localhost,ваш_домен
    Вместо xxx.xxx.xxx.xxx укажите IP сервера, а вместо <ваш_домен> – доменное имя.
```

# Описание используемых технологий
 - Python 3.9
 - Django==3.2.3
 - djangorestframework==3.12.4
 - Nginx
 - Gunicorn
 - Node.js
 - PostgreSQL
 - Docker
 - CI/CD
# Автор:
Павлов Дмитрий - [GitHub](https://github.com/pavlovbjj)