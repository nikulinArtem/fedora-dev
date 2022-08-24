# fedora-dev
Установка всех необходимых инструментов для web-разработки на Red Hat Fedora 36


Подготовка системы после начальной установки:

#1 Ускоряем DNF: 

    $ sudo nano /etc/dnf/dnf.conf
    
    И добавляем эти параметры:

    skip_if_unavailable=True
    fastestmirror=True
    max_parallel_downloads=10
    defaultyes=True
    keepcache=True

    Чтобы сохранить изменения: Ctrl+O — Enter — Ctrl+X

    $ sudo dnf autoremove
    $ sudo dnf clean all

#2 Обновляем систему и пакеты до актуальной версии:

    $ sudo dnf upgrade --refresh

#3 Доп. настройки GNOME:

    $ sudo dnf install gnome-tweaks

#4 Менеджер расширений GNOME

    $ flatpak install flathub com.mattjakeman.ExtensionManager


Установка инструментов для разработки:

#1 Node.js:
    Установка:

    $ sudo dnf install nodejs

    Установка глобальных модулей (по ситуации):
    
    $ mkdir ~/.npm-global

    $ npm config set prefix '~/.npm-global'
    
    $ export PATH=~/.npm-global/bin:$PATH
    
    $ source ~/.profile



#2 Yarn:

    $ sudo dnf install yarnpkg

#3 PHP/php-cli

    $ sudo dnf -y update

    $ sudo dnf -y install http://rpms.remirepo.net/fedora/remi-release-36.rpm

    $ sudo dnf -y module reset php

    $ sudo dnf -y install dnf-plugins-core
    
    $sudo dnf config-manager --set-enabled remi

    $ sudo dnf -y module install php:remi-8.1

#4 Composer:

    $ curl -sS https://getcomposer.org/installer | php  

    $ sudo mv composer.phar /usr/local/bin/composer  
    
    $ sudo chmod +x /usr/local/bin/composer  

    $ composer -v

#5 Docker:

    1. Установка Docker Engine:

        $ sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine

        $ sudo dnf -y install dnf-plugins-core
        
        $ sudo dnf config-manager \
                        --add-repo \
                        https://download.docker.com/linux/fedora/docker-ce.repo

        $ sudo dnf install docker-ce docker-ce-cli containerd.io docker-compose-plugin

    2. Установка Docker Desktop

        а) Перед установкой необходимо установить Docker Engine (инструкция выше)

        b) Скачать свежий rpm-пакет:

        https://desktop.docker.com/linux/main/amd64/docker-desktop-4.11.0-x86_64.rpm?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64

        c) Установить по команде:

            $ sudo dnf install ./docker-desktop-<version>-<arch>.rpm

    Docker Desktop установлен.

    Далее возникает ошибка при попытке залогиниться в приложении.

    Решение:

        $ sudo apt-get install pass

        если не сработает, то

        $ sudo yum install pass

        далее

        $ wget https://github.com/docker/docker-credential-helpers/releases/download/v0.6.0/docker-credential-pass-v0.6.0-amd64.tar.gz && tar -xf docker-credential-pass-v0.6.0-amd64.tar.gz && chmod +x docker-credential-pass && sudo mv docker-credential-pass /usr/local/bin/

        $ gpg2 --gen-key

        $ pass init "<Your Name>"

        $ sed -i '0,/{/s/{/{\n\t"credsStore": "pass",/' ~/.docker/config.json

        $ docker login


#6 XAMPP 

    Установка XAMPP:

        Скачать файл по ссылке (версия 7.4.29 / PHP 7.4.29 лучше всего подходит для Битрикса и WordPress):

            https://www.apachefriends.org/ru/download.html

        Открыть папку загрузок и через терминал этой папки назачить расшиненные права на установщик:

            Измените разрешения для установщика

            $ chmod 755 xampp-linux-*-installer.run

        Запустите установщик:

            $ sudo ./xampp-linux-*-installer.run

        Вот и всё. XAMPP теперь установлен под каталогом /opt/lampp.

    Решение проблем после установки:

        Часто Apache Web Server остановлен после первой установки, решение:

            $ sudo service apache2 stop

            либо

            $ sudo apachectl stop

        После этого перезапустить сервер или устройсво, должно заработать.

    Настройка виртуальных хостов, чтобы запускать несколько проектов сразу:

        1. Открываем конфигурационный файл — /Applications/XAMPP/xamppfiles/etc/httpd.conf

            Команда $ sudo gedit /opt/lampp/etc/httpd.conf

        1.1. Изменить User daemon на User username (стр. 173)

            171      #running httpd, as with most system services. 
            172      # 
            173      User daemon 
            174      Group daemon 
            175  </IfModule> 

        1.2. Включаем VirtualHosts — раскомментировав (стр. 488)

            Include etc/extra/httpd-vhosts.conf

                487    #Virtual hosts
                488    #Include etc/extra/httpd-vhosts.conf

            убрать решетку перед Include

                487    #Virtual hosts                  
                488    Include etc/extra/httpd-vhosts.conf 

        2. Добавление своих хостов — открываем файл — /Applications/XAMPP/etc/extra/httpd-vhosts.conf

            Команда $ sudo gedit /opt/lampp/etc/extra/httpd-vhosts.conf

        2.1. Чтобы оставить локальный хост работающим без изменений

            #localhost 
            <VirtualHost *:80> 
                ServerName localhost 
                DocumentRoot "/Applications/XAMPP/xamppfiles/htdocs" 
                <Directory "/Applications/XAMPP/xamppfiles/htdocs"> 
                    Options Indexes FollowSymLinks Includes execCGI 
                    AllowOverride All 
                    Allow From All 
                    Order Allow,Deny 
                </Directory> 
            </VirtualHost> 

        2.2. Подключить свой сайт — cоздать папку сайта site.local (пример)
        username — ваше имя пользователя в системе Mac OS X
        folder — пусть к папке сайта
        site.local — папка сайта

            #My custom host 
            <VirtualHost *:80> 
                ServerName site.local 
                DocumentRoot "/Users/username/folder/site.local" 
                <Directory "/Users/username/folder/site.local"> 
                    Options Indexes FollowSymLinks Includes ExecCGI 
                    AllowOverride All 
                    Require all granted 
                </Directory> 
                ErrorLog "logs/site.local-error_log" 
            </VirtualHost> 

        Директиву AllowOverride None обязательно заменяем на AllowOverride All, иначе файл .htaccess не будет прочитан сервером Apache

        2.2. Подключить свой сайт — cоздать папку сайта site.local (пример)
            username — ваше имя пользователя в системе Mac OS X
            folder — пусть к папке сайта
            site.local — папка сайта

            #My custom host 
            <VirtualHost *:80> 
                ServerName site.local 
                DocumentRoot "/Users/username/folder/site.local" 
                <Directory "/Users/username/folder/site.local"> 
                    Options Indexes FollowSymLinks Includes ExecCGI 
                    AllowOverride All 
                    Require all granted 
                </Directory> 
                ErrorLog "logs/site.local-error_log" 
            </VirtualHost> 

            Директиву AllowOverride None обязательно заменяем на AllowOverride All, иначе файл .htaccess не будет прочитан сервером Apache

            !ВАЖНО: вместо * можно прописать отностительный путь, например: 127.0.0.1, 127.0.0.2 и т.д., это путь должен совпадать с тем, 
                    что вы указываете в файле ниже.

        3. Необходимо указать системе Mac OS X, где находятся виртуальные сайты.
        В терминале вводим команду:

            $ sudo gedit /etc/hosts

            Добавляем в конец файла hosts строчку: 127.0.0.1 site.local

            #XAMPP    VirtualHost  
            127.0.0.1        site.local 

            Перезапускаем XAMPP и виртуальные хосты добавлены.
            Если пути в последних двух файлах совпадают, то можно открывать сайт через относительный путь (127.0.0.1) или по названию.
            Папка /logs дожна присутствовать в каждой рабочей директории иначе будет ошибка. 

            по localhost — открывается XAMPP
            по site.local — открывается уже наш сайт.


