# О двіжке OpenVK 
<img align="right" src="https://github.com/openvk/openvk/raw/master/Web/static/img/logo_shadow.png" alt="openvk" title="openvk" width="15%">
**OpenVK** спроба зробити простой CMS, якій імітує старий ВК. Наданий тут код ще не стабільний.

ВКонтакте належить Павлу Дурову і VK Group.

Чесно кажучи, ми навіть не знаємо, чи це взагалі працює. Однак ця версія підтримується, і ми будемо раді прийняти ваші повідомлення про помилки [у нашої bugtracker-сістеми](https://github.com/openvk/openvk/projects/1). Ви також маєте можливість подати їх за допомогою [системи квитків](https://openvk.su/support?act=new) (вам треба аккаунт OVK для цього).

## Коли реліз?

Будь ласка, користуйтеся ветку `master`, тому що там більше всього зміненій.

Оновлення вихідного коду виконується за допомогою цієї команди: `git pull --recurse-submodules`

## Інстанції

* **[openvk.su](https://openvk.su/)**
* [social.fetbuk.ru](http://social.fetbuk.ru/)

## Могу-ль я зробити свою інстанцію OpenVK?

Так! І ми будемо тількі ради!
Однак OVK використовує Chandler Application Server. Для цього програмного забезпечення потрібні розширення, які можуть не надаватися вашим хостинг-провайдером (а саме sodium і yaml. Ці розширення доступні на більшості хостингів ISPManager).
Якщо ви хочете, ви можете додати свою інстанцію до списку вище, щоб люди могли там зареєструватися.

### Встановлення

1. Встановіть PHP 7, веб-сервер, Composer, Node.js, Yarn і [Chandler](https://github.com/openvk/chandler)
   * PHP 8 ще **не** перевірено, тому не варто очікувати, що він працюватиме.
2. Установіть [commitcaptcha](https://github.com/openvk/commitcaptcha) і OpenVK як розширення Chandler, як це:
```
git clone --recursive https://github.com/openvk/openvk /path/to/chandler/extensions/available/openvk
git clone https://github.com/openvk/commitcaptcha /path/to/chandler/extensions/available/commitcaptcha
```
3. І ввімкніть їх:
```
ln -s /path/to/chandler/extensions/available/commitcaptcha /path/to/chandler/extensions/enabled/
ln -s /path/to/chandler/extensions/available/openvk /path/to/chandler/extensions/enabled/
```
4. Імпортуйте `install/init-static-db.sql` до **тої самої бази даних**, до якої ви встановили Chandler
5. Імпортуйте `install/init-event-db.sql` до **окремої бази даних**
6. Скопіюйте `openvk-example.yml` до `openvk.yml` і змініть параметри
7. Запустіть `composer install` у каталозі OpenVK
8. Перейдіть до `Web/static/js` і виконайте `yarn install`
9. Встановіть `openvk` як вашу кореневу програму в `chandler.yml`

**Примітка**: якщо підмодулі OVK не були завантажені заздалегідь (тобто `--recursive` не використовувався під час клонування), цю команду *потрібно* виконати в папці `openvk`: `git submodule update --init`

Після цього ви можете увійти як системний адміністратор у самій мережі (реєстрація не потрібна):

* **Вхід**: `admin@localhost.localdomain6`
* **Пароль**: `admin`
   * Рекомендується змінити пароль перед використанням вбудованого облікового запису.

Повний приклад інсталяції для CentOS 8 також доступний [тут](/openvk_engine/centos8_installation.md).

### Якщо мій веб-сайт використовує OpenVK, чи варто мені публікувати його вихідні коди?

Вас заохочують до цього. Але ми цього не виконуємо. Ви можете залишити свої джерела при собі (якщо тільки ви не поширюєте свій дистрибутив OpenVK іншим людям).
Вам також не потрібно публікувати вихідні тексти ваших пакетів тем і плагінів.

## Де я можу отримати допомогу?

Ви можете зв'язатися з нами через:

* [Bugtracker](https://github.com/openvk/openvk/projects/1)
* [Система квитків](https://openvk.su/support?act=new)
* Чат Telegram: перейдіть на [наш канал](https://t.me/openvk) і відкрийте обговорення в меню нашого каналу.
* [Reddit](https://www.reddit.com/r/openvk/)
* [Обговорення](https://github.com/openvk/openvk/discussions)

!!! УВАГА
     Багтрекер і чат Telegram є публічними місцями. А систему квитків обслуговують волонтери. Якщо вам потрібно повідомити про щось, що не слід негайно розголошувати широкому загалу (наприклад, звіт про вразливість), зв’яжіться з нами безпосередньо за цією електронною адресою: **openvk [at] tutanota [dot] com**
