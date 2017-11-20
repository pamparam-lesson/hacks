Установка Apache Ubuntu 16.04
-----------------------------

Установить Apache в Ubuntu очень просто, для этого достаточно выполнить:
 `sudo apt-get install apache2`

После установки добавим программу в автозагрузку:
 `sudo systemctl enable apache2`
	
`sudo gedit /etc/apache2/apache2.conf`


Здесь ищем теги _<Directory>_ (это легко сделать встроенным в nano поиском;   
 вызывается он комбинацией клавиш, или, как говорят, хоткеем Ctrl+w) и под последним из них дописываем
	
<Directory /home/user/public_html>    
  AllowOverride All    
  Require all granted      
</Directory>

Здесь мы побежали немного впереди паровоза, но раз уж нам пришлось открыть этот файл, то все изменения сразу и внесем. Яснее станет позже, пока объясню вкратце.
 Мы прописали опции общей директории (папки) для всех наших сайтов, а именно: разрешили использовать для нее файл .htaccess 
 (для ЧПУ, например; но не только) и предоставили права доступа (через веб-сервер) для всех.

Дальше хоткеем Alt+/ идем в конец файла и дописываем здесь

	
**ServerName localhost**

Сохраняем хоткеем Ctrl+o, подтверждаем энтером и выходим из nano (Ctrl+x).

Поскольку в подавляющем большинстве (если не у всех) у наших сайтов дефолтной страницей будет index.php, а не index.html, то открываем следующий файл.
	
`sudo gedit /etc/apache2/mods-available/dir.conf`

И здесь index.php помещаем в начало строки DirectoryIndex, перед index.html. Должно получиться так.
	
`<IfModule mod_dir.c>`
 ` DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`
`</IfModule>`

Сохраняем и закрываем. 

**php.ini** ------------------------------------------------

Ну а теперь поковыряемся в конфигурационном файле PHP, находящемся по адресу **/etc/php/7.0/apache2/php.ini**. Да не просто поковыряемся, а и кое-что поменяем.

	
`sudo gedit /etc/php/7.0/apache2/php.ini`
Короткая форма записи
	
`short_open_tag = Off`

Это значит, что по умолчанию запрещено использовать короткую форму записи тегов, т.е. <?, а не <?php. И хотя мне нечасто приходится встречать эту форму (сам я ее не использую), иногда все же на некоторых сайтах попадается. Поэтому имеем в виду. Также имеем в виду, что ее включение не позволит включать xml непосредственно в php (только через echo).
Показ ошибок
	
`display_errors = Off`

Отключен показ ошибок. Вполне естественно для рабочих сайтов - посетителям (среди которых и взломщики попадаются) совсем не нужно видеть ошибки (а тем более предупреждения). На нашем же тестовом сервере желательно этот параметр включить. Хотя иногда он может здорово мешать, поэтому здесь индивидуально. В конце концов ошибки можно и в журнале посмотреть. Если хотим выводить - ставим On.
	
`display_startap_errors = Off`

Показ ошибок при запуске. Если нужно отследить ошибки при запуске PHP, меняем на **On**.

	
`track_errors = Off`

Если поставим **On**, то последняя произошедшая ошибка будет первой в переменной **php_errormsg.**

Сохраняем и закрываем.
Размеры файлов

По умолчанию максимальный размер загружаемого файла ограничен 2 МБ. Это касается и импорта баз данных в phpMyAdmin. А базы довольно часто бывают больше, и даже значительно. В крупных интернет-магазинах база размером 40 МБ - не редкость. Но даже этот размер меркнет перед базой размером более 800 МБ, с которой мне пришлось однажды столкнуться. Там изображения хранились прямо в базе - в формате BLOB. Поэтому в случае, если нам придется работать с подобными базами, меняем следующие параметры:
	
`upload_max_filesize = 2M
post_max_size = 8M`

Устанавливаем устраивающие нас значения.

Вполне возможно, придется поменять еще и
	
`max_execution_time = 30`  

И запустим веб-сервер сейчас:   
 `sudo systemctl start apache2`
Теперь можно проверить что получилось, откройте браузер и наберите в адресной строке localhost:

Создайте новую папку для нашего виртуального хоста:
 `sudo mkdir /var/www/test.site`

Дадим права на доступ:
 `sudo chmod -R 755 /var/www`

Теперь можно добавлять виртуальный хост, для этого создайте файл и наполните его содержимым:
 `sudo gedit /etc/apache2/sites-available/test.site.conf`

`<VirtualHost *:80>
ServerName test.site
ServerAlias www.test.site
ServerAdmin webmaster@localhost
DocumentRoot /var/www/test.site/public_html
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

переносим все хосты из одной папки в другую
`sudo cp /media/pamparam/progi/sites-available/* /etc/apache2/sites-available -R -n -v`

Теперь сохраните файл, далее нужно активировать наш хост:
 `sudo a2ensite test.site.conf`

Перезапускаем веб сервер:
 `sudo systemctl restart apache2`

Теперь необходимо завернуть трафик с этого домена на локальный сервер, 
для этого добавьте строчку в **/etc/hosts**:
 `sudo gedit /etc/hosts`
**127.0.0.1 test.site**



ЧПУ
--------------------------------
В большинстве случаев нам придется работать с ЧПУ-ссылками. Поэтому необходимо подключить модуль Апача mod_rewrite.
	
`sudo a2enmod rewrite
sudo service apache2 restart`



Установка php 7 Ubuntu 16.04
---------------------------------------
 выполняется следующей командой:        
 `sudo apt-get install php7.0-mysql php7.0-curl php7.0-json php7.0-cgi  php7.0 libapache2-mod-php7.0`

Если вы хотите установить все доступные модули php, чтобы в будущем не было проблем, вы можете выполнить команду:
 `sudo apt-get install php*`

После завершения установки проверим версию php:
 `php -v`

**включить отображение ошибок в php**
 
Для этого откройте файл **/etc/php/7.0/apache2/php.ini**, найдите строку **display_errors = Off** и поменяйте **off** на **on**:
 **sudo gedit /etc/php/7.0/apache2/php.ini**

Редакция php.ini:

Найдите строку:
    `post_max_size = 8M`
    Увеличьте до 16 Мбайт максимальный размер данных принимаемых методом POST, изменив ее на:
   ` post_max_size = 16M`
    Найдите строку:
    `;include_path = ".;c:\php\includes"`
    Раскомментируйте ее, убрав перед строкой символ точки с запятой.
    (Внимание исключение! Обратные косые черты при указании пути):
    `include_path = ".;c:\php\includes"`
    Создайте пустой каталог "C:\php\includes", для хранения подключаемых классов.
    Найдите строку:
    `extension_dir = "./"`
    Установите значением данной директивы путь к папке с расширениями:
    `extension_dir = "C:/php/ext"`
    Найдите строку:
    `;upload_tmp_dir =`
    Раскомментируйте ее и укажите в значении следующий путь:
    `upload_tmp_dir = "C:/php/upload"`
    Создайте пустую папку "C:\php\upload", для хранения временных файлов загружаемых через HTTP.
    Найдите строку:
   ` upload_max_filesize = 2M`
    Увеличьте максимально допустимый размер загружаемых файлов до 16 Мбайт:
    `upload_max_filesize = 16M`
    Подключите, расскомментировав, данные библиотеки расширений:???????подумать несколько раз
    `extension=php_bz2.dll
    extension=php_curl.dll
    extension=php_gd2.dll
    extension=php_mbstring.dll
    extension=php_mysql.dll
    extension=php_mysqli.dll`
    Найдите строку:
    `;date.timezone =`
    Раскомментируйте и установите значением часовой пояс вашего местоположения (список часовых поясов можно найти в документации):
    `date.timezone = "Europe/Moscow"`
    Найдите строку:
   `;session.save_path = "/tmp"`
    Раскомментируйте и установите значением данной директивы такой путь:
    `session.save_path = "C:/php/tmp"`
    Создайте пустую папку "C:\php\tmp" для хранения временных файлов сессий.


`sudo systemctl restart apache2`


Установка Mysql server на ubuntu 16.04
-------------------------------------------

вводим в терменале комманду:     
`sudo apt-get install mysql-server mysql-client php7.0-mysql`

во время установки нужно будет ввести пароль для root 


Установка phpmyadmin ubuntu 16.04
-------------------------------------

Установка и настройка lamp в ubuntu 16.04 также будет включать установку Phpmyadmin. Phpmyadmin - это бесплатный инструмент, с открытым исходным кодом, для реализации веб-интерфейса управления базами данных MySQL. Он доступен в официальных репозиториях Ubuntu 16.04, установим его с помощью команды:
 `sudo apt-get install phpmyadmin php-mbstring php-gettext`

Но тут уже во время установки потребуется немного конфигурации. Сначала нужно выбрать наш веб-сервер:
Для перемещения по пунктам используйте стрелки вверх/вниз, для выбора пробел, для переключения - Tab.
В следующем окне мастера нам предлагают настроить базу данных phpmyadmin, соглашаемся:

Вводим пароль, который будет использован для подключения phpadmin к базе данных
Подтверждение пароля:

`sudo apt-get install php7.0-mbstring php7.0-gettext php7.0-mcrypt`

После этого нужно включить эти модули

`sudo phpenmod mcrypt
 sudo phpenmod mbstring`

И перезапустить apache2

`sudo systemctl restart apache2`

Когда установка phpmyadmin ubuntu 16.04 будет завершена откройте браузер и наберите в адресной строке localhost/phpmyadmin:


Устанавливаем Git
-----------------------
1 sudo apt-get install git

Настраиваем.
	
`git config --global user.name "User Name"
git config --global user.email "username@gmail.com"`

Здесь username - ваше имя, а username@gmail.com - ваша существующая почта. В общем-то, это все. Об использовании Git в ближайшем будущем надеюсь написать отдельную статью


Устанавливаем Composer
-----------------------------------
Сначала установите curl модуль, что позволит нам легко установить Composer:

`sudo apt-get install curl`

Затем установите Composer:
`cd ~
curl -sS https://getcomposer.org/installer | php`

Для упрощения его использования, мы сделаем его исполняемым в любом месте системы:

`sudo mv composer.phar /usr/local/bin/composer`

Теперь, чтобы использовать composer, просто введите в теминале:

`composer`


устанавливаем плагин для yii
-------------------------------------
`composer global require "fxp/composer-asset-plugin:^1.2.0"`
обновление плагина
`composer global require fxp/composer-asset-plugin --no-plugins`
<VirtualHost *:80>
ServerAdmin admin@zululu
    DocumentRoot "/media/pamparam/progi/OpenServer/domains/zululu/web"
    ServerName zululu
    ServerAlias zululu
    ErrorLog "/media/pamparam/progi/OpenServer/domains/zululu/logs/zululu-error_log"
    CustomLog "/media/pamparam/progi/OpenServer/domains/zululu/logs/zululu-access_log" common
<Directory "/media/pamparam/progi/OpenServer/domains/zululu">
Options Indexes FollowSymLinks Includes ExecCGI
        AllowOverride All
        Require all granted
</Directory>
</VirtualHost>

настройка хоста и php.ini 

-----------------------------------------


Сменить владельца для всех папок и файлов, лежащих в папке

    sudo chown -R user:group /path/to/dir/

Где user - новый владелец, а group - группа пользователя.

-R указывает на то, что права необходимо сменить рекурсивно.
Сменить права доступа на файл или папку

    chmod -R 700 /path/to/file/or/dir

-R указывает на то, что права необходимо сменить рекурсивно.
Смена прав доступа только для файлов

    find /path/to/dir -type f -exec chmod 600 {} \;

Смена прав доступа только для папок

    find /path/to/dir -type d -exec chmod 700 {} \;


