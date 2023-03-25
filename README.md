# Docker Compose

This is a Docker Compose configuration for quickly and easily spinning up a test / development environment for CRMEB-MER(多商户) version. 

This compose file defines an application with four services: `nginx`, `php`, `MySQL`, `Redis`. The image for `php` is built with the `Dockerfile` inside the `php` directory. It builds on top of php `7.3`,  with `Supervisor` and `Swoole` required by CRMEB.

When deploying the application, docker compose maps the container port `80` to port `28138` of the host as specified in the file. Make sure port `28138` on the host is not occupied, otherwise the port should be changed.

## Prerequisites

Minimum requirement is to have **Docker**, **Docker Compose** and **Git** installed. Debian / Ubuntu OS is recommended.

This setup has been tested on both vanilla and WSL versions of Ubuntu Server 20.04 with following versions of applications.

- Docker version 20.10.21
- Docker Compose version v2.13.0
- Git version 2.25.1

Earlier versions may also work but haven’t been tested.

## How to use

1. Download CRMEB-MER application code, unzip it to `crmeb-mer` folder.
   <aside>
    💡 You can use any other folder than `crmeb-mer`, just be sure to change the .env file in the docker compose folder.
   </aside>


2. Deal with CRMEB-MER's 'encrypted' files according to php version, in this case, `7.3`.
   ```bash
   cd crmeb-mer
   unzip install/compiled/compiled73.zip
   mv crmeb.php ./config/
   mv basic ./crmeb/
   cd ..
   ```

3. Now on to docker compose. Clone the repo
    
    ```bash
    git clone https://github.com/shdigitech/docker4crmeb-mer.git
    ```

4. Create environment file
    ```bash
    cp .env.default .env
    ```
    
5. Check you have the folder structure like this(both repos sit on the same level)
    
    ```bash
    .
    ├── crmeb-mer
    └── docker4crmeb-mer
    ```
    <aside>
    💡 if you have put CRMEB-MER application code to another folder, change the `PATH_CRMEB` variable in the `.env` file accordingly.
    </aside>

6. Enter the docker-compose folder and spin up containers
    ```bash
    cd docker4crmeb-mer
    docker compose --profile dev up -d
    ```
    
    <aside>
    💡 Note it is assumed the production environment is running a dedicated database and does not need a mysql service in this docker compose, so it doesn't start one by default. In Dev environment you can specify the `dev` profile either in the command line or in the `.env` file (`'export COMPOSE_PROFILES=dev'`) in order to spin up MySql and PHPMYADMIN services. 
    💡 First time build might take a while depending on your hardware configuration, be patient.    

    💡 Omitting the `-d` parameter will output a bunch of logs on the console, which could be helpful for debugging. Note that pressing Ctrl + C or closing the console window will shutdown all containers.
    💡 Note there are two profiles for Production and Dev environments respectively. MySql and PHPMYADMIN services will only be available in Dev profile. 
    </aside>
    
7. Now open your favorite browser and navigate to
    
    [http://localhost:28238/](http://localhost:28238/)
    
    You shall see the installation wizard page of CRMEB
    <aside>
    💡 You can also change the port in the `.env` file accordingly.
    </aside>
    
8. Configuration for services(other fields can be left default)
    
    
    | 数据库MySql配置 |           |
    | --------------- | --------- |
    | 数据库服务器    | mysql     |
    | 数据库端口      | 3306      |
    | 数据库用户名    | crmeb_mer |
    | 数据库密码      | crmeb_mer |
    | 数据库名        | crmeb_mer |
    
    | Redis配置  |       |
    | ---------- | ----- |
    | 服务器地址 | redis |
    | 端口号     | 6379  |
    
    <aside>
    💡 You can also find / change these values in the `.env` file.
    </aside>

9.  After installation finishes, it is needed to restart the php services for CRMEB-MER to work.
    ```bash
    docker compose restart php-fpm
    ```

10. After restarting, you can access the frontend site and admin site respectively at
    1. [http://localhost:28238](http://localhost:28238)
    2. [http://localhost:28238/admin](http://localhost:28238/admin)
    
    <aside>
    💡 Note: it may take a while for the front end site to load.
    </aside>
    
11. This docker-compose combo also packs a PHPMYADMIN for easy database access, you can find it at
    
    [http://localhost:28239/](http://localhost:28239/)
    
12. To shut everything down
    
    ```bash
    docker compose down
    ``` 
