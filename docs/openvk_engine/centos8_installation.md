<!-- ермаков гей-->

# Установка OpenVK на CentOS 8

Це приклад посібника, розробленого спеціально для CentOS 8, який базується на способі [@rem-pai](https://github.com/rem-pai) для встановлення OpenVK, модифікованому WerySkok.

!!! УВАГА
    Термін експлуатації CentOS 8 скоро закінчиться. Існують інші підтримувані подібні дистрибутиви, наприклад Rocky Linux або AlmaLinux.

## SELinux

🖥Виконайте команду:

```bash
sestatus
```

Якщо там зазначено `Status SELinux: enabled`, тоді SELinux заважатиме нам. Давайте відключимо його.

!!! ЗАУВАЖТЕ
    я знаю, що це не найбезпечніше рішення, але я не знаю жодного правильного способу, який би спрацював._.

📝Відредагуйте файл `/etc/sysconfig/selinux` і змініть рядок `SELinux=enforcing` на `SELinux=disabled`, потім 🔌перезавантажте машину. `sestatus` має повідомляти про `Status SELinux: disabled`

## Залежності

🖥Давайте встановимо репозиторії EPEL і Remi для PHP 7.4:

```bash
dnf -y install epel-release
dnf -y install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
```

🖥Тоді ввімкнемо потрібні модулі:

```bash
dnf -y module enable php:remi-7.4
dnf -y module enable nodejs:14
```

🖥І встановимо залежності:

```bash
dnf -y install php php-cli php-common unzip php-zip php-yaml php-gd php-pdo_mysql nodejs git
```

🖥Не забудьте про Yarn і Composer:

```bash
npm i -g yarn
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php --filename=composer2 --install-dir=/bin --snapshot
rm composer-setup.php
```

### База данніх

🖥Ми будемо використовувати Percona Server для БД:

```bash
dnf -y install https://repo.percona.com/yum/percona-release-latest.noarch.rpm
percona-release setup -y ps80
dnf -y install percona-server-server percona-toolkit
systemctl start mysql
```

🖥А потім знайдіте тимчасовий пароль:
```bash
cat /var/log/mysqld.log | grep password
```

Він довжен віглядати як:

    2021-01-11T12:56:09.203991Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: >b?Q.fDXJ4fk

🖥Потім запустіте mysql_secure_installation, установіте новий пароль і дайте відповідь так:

    Change the password for root ? ((Press y|Y for Yes, any other key for No) : n
    Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
    Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
    Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
    Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y

### ffmpeg

Крім того, ви можете встановити ffmpeg для обробки відео.

🖥Вам знадобиться використовувати репозиторій RPMFusion, щоб його встановити:

```bash
dnf -y localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-8.noarch.rpm
dnf -y install --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-8.noarch.rpm
```

🖥Потім встановіте SDL2 і ffmpeg:

```bash
dnf -y install http://rpmfind.net/linux/epel/7/x86_64/Packages/s/SDL2-2.0.10-1.el7.x86_64.rpm
dnf -y install ffmpeg
```

## Встановлення Chandler та OpenVK

🖥Встановіте Chandler в `/opt`:

```bash
cd /opt
git clone https://github.com/openvk/chandler.git
cd chandler/
composer2 install
```

🖥Вам знадобиться секретний ключ. Ви можете створити його за допомогою:

```bash
cat /dev/random | tr -dc 'a-z0-9' | fold -w 128 | head -n 1
```

📝Тепер відредагуйте конфігураційний файл chandler-example.yml так:

```yaml title="/opt/chandler/chandler-example.yml" hl_lines="12-14 17"
chandler:
    debug: true
    websiteUrl: null
    rootApp:    "openvk"
    
    preferences:
        appendExtension: "xhtml"
        adminUrl: "/chandlerd"
        exposeChandler: true
    
    database:
        dsn: "mysql:unix_socket=/var/lib/mysql/mysql.sock;dbname=db"
        user: "root"
        password: "DATABASE_PASSWORD"
    
    security:
        secret: "SECRET_KEY_HERE"
        csrfProtection: "permissive"
        sessionDuration: 14
```

🖥І перейменуйте його на `chandler.yml`:

```bash
mv chandler-example.yml chandler.yml
```

🖥Тепер давайте встановимо розширення CommitCaptcha. Він є обов'язковим для OpenVK.

```bash
cd extensions/available/
git clone https://github.com/openvk/commitcaptcha.git
cd commitcaptcha/
composer2 install
```

🖥А тепер завантажте OpenVK:

```bash
cd ..
git clone --recursive https://github.com/openvk/openvk.git
cd openvk/
composer2 install
cd Web/static/js
yarn install
```

📝Тепер відредагуйте конфігураційний файл openvk-example.yml так:

```yaml
openvk:
    debug: true
    appearance:
        name: "OpenVK"
        motd: "Yet another OpenVK instance"
    preferences:
        femaleGenderPriority: true
        uploads:
            disableLargeUploads: false
            mode: "basic"
        shortcodes:
            forbiddenNames:
                - "index.php"
        security:
            requireEmail: false
            requirePhone: false
            forcePhoneVerification: false
            forceEmailVerification: false
            enableSu: true
            rateLimits:
                actions: 5
                time: 20
                maxViolations: 50
                maxViolationsAge: 120
                autoban: true
        support:
            supportName: "Moderator"
            adminAccount: 1 # Change this ok
        messages:
            strict: false
        wall:
            postSizes:
                maxSize: 60000
                processingLimit: 3000
                emojiProcessingLimit: 1000
        menu:
            links:
                
        adPoster:
            enable: false
            src: "https://example.org/ad_poster.jpeg"
            caption: "Ad caption"
            link: "https://example.org/product.aspx?id=10&from=ovk"
        bellsAndWhistles:
            fartscroll: false
            testLabel: false
    credentials:
        smsc:
            enable: false
            client: ""
            secret: ""
        eventDB:
            enable: true # Better enable this
            database:
                dsn: "mysql:unix_socket=/var/lib/mysql/mysql.sock;dbname=openvk_eventdb"
                user: "root"
                password: "DATABASE_PASSWORD"
```

Зверніть увагу на розділ `eventDB`, оскільки краще ввімкнути базу даних подій.

🖥І перейменуйте його на `openvk.yml`:

```bash
mv openvk-example.yml openvk.yml
```

🖥Потім увімкніте CommitCaptcha та OpenVK для Chandler:

```bash
ln -s /opt/chandler/extensions/available/commitcaptcha/ /opt/chandler/extensions/enabled/commitcaptcha
ln -s /opt/chandler/extensions/available/openvk/ /opt/chandler/extensions/enabled/openvk
```

### Конфігурація БД

!!! Зауважте
     Краще створити іншого користувача для SQL, але я не буду розповідати про це.

🖥Введіть оболонку MySQL:

```bash
mysql -p
```

🖥І створити бази даних основних і подій:

```sql
CREATE DATABASE openvk;                                                                                          
CREATE DATABASE openvk_eventdb;
exit
```

🖥Перейдіте до `/opt/chandler`:

```bash
cd /opt/chandler
```

📝Нам потрібно імпортувати базу даних Chandler, але з якоїсь причини вона не готова до використання, тому нам потрібно відредагувати дамп `install/init-db.sql`:
1\. Видаліть ` PAGE_CHECKSUM=1` всюди.
2\. Скрізь замініть `Aria` на `InnoDB`.

🖥Тепер дамп бази даних можна імпортувати:

```bash
mysql -p'DATABASE_PASSWORD' openvk < install/init-db.sql
```

🖥Перейдіть до `extensions/available/openvk/`:

```bash
cd extensions/available/openvk/
```

📝Нам також потрібно імпортувати базу даних OpenVK. Якщо ви не використовуєте MariaDB (тут ми використовуємо Percona), ви повинні редагувати `install/init-static-db.sql`:
1\. Скрізь замініть `utf8mb4_unicode_nopad_ci` на `utf8mb4_unicode_520_ci`.

🖥Тепер дамп бази даних можна імпортувати:

```bash
mysql -p'DATABASE_PASSWORD' openvk < install/init-static-db.sql
```

🖥Також імпортуйте БД подій:

```bash
mysql -p'DATABASE_PASSWORD' openvk_eventdb < install/init-event-db.sql
```

### Заванжатення веб-сервера:

Apache вже встановлено, тому ми будемо ним користуватися.

🖥Зробіть користувача `apache` власником папки `chandler`:

```bash
cd /opt
chown -R apache: chandler/
```

📝Тепер давайте створимо конфігураційний файл `/etc/httpd/conf.d/10-openvk.conf`:

```apache
<VirtualHost *:80>
    ServerName openvk.local
    DocumentRoot /opt/chandler/htdocs

    <Directory /opt/chandler/htdocs>
        AllowOverride All

        Require all granted
    </Directory>

    ErrorLog /var/log/openvk/error.log
    CustomLog /var/log/openvk/access.log combinedio
</VirtualHost>
```

📝Також увімкніте rewrite_module, створивши `/etc/httpd/conf.modules.d/02-rewrite.conf`:

```apache
LoadModule rewrite_module modules/mod_rewrite.so
```

🖥Створіте каталог для журналів OpenVK і зробіте його власником користувача `apache`:

```bash
mkdir /var/log/openvk
chown apache: /var/log/openvk/
```

🖥Зробіте виняток брандмауера для порту 80:

```bash
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```

🖥І запустіте Apache:

```bash
systemctl start httpd
```

OpenVK має працювати прямо зараз!

Також ви можете збільшити ліміт файлу на 2 Мб, відредагувавши `/etc/php.ini`. Крім того, краще встановити PHPMyAdmin, але я не буду розповідати про це.
