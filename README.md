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

        b) [Скачать свежий rpm-пакет](https://desktop.docker.com/linux/main/amd64/docker-desktop-4.11.0-x86_64.rpm?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64)

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


Happy hacking!





