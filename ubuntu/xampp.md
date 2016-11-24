## Установка Xampp 
в директории со скачанным пакетом надо дать команду

**chmod +x xampp-linux-*-installer.run**
а затем запустить инсталлятор

**sudo ./xampp-linux-*-installer.run**

### удаление

Для полного и исчерпывающего удаления XAMPP с компа нужно просто-напросто удалить каталог /opt/lampp со всем содержимым:

**sudo rm -rf /opt/lampp**


## Настройка 

Правим sudo gedit /etc/hosts

Предварительное Note: Будьте умницей, сделайте бэкап первоначального /etc/hosts. Бережёного бог бережет.

Файл /etc/hosts приведём к такому виду:

127.0.0.1   localhost lampp xampp mysite1.dev mysite2.dev mysite3.dev
127.0.1.1   `<hostname>` # Вместо `<hostname>` подставьте имя своего компьютера, которое выв

Для создания своего хоста Apache VirtualHosts

1. Правим sudo gedit /opt/lampp/etc/httpd.conf 

####1.1. Изменить User daemon на User username (стр. 173) 

171      #running httpd, as with most system services.
172      # 
173      User daemon 
174      Group daemon 
175  `</IfModule>` 

где username — имя вашего пользователя в системе Mac OS X
171      #running httpd, as with most system services. 
172      # 
173      User username
174      Group daemon 
175  `</IfModule>`  


####1.2. Включаем VirtualHosts — раскомментировав (стр. 488)
**Правим sudo gedit /opt/lampp/etc/httpd.conf**

487    #Virtual hosts
488    #Include etc/extra/httpd-vhosts.conf

убрать решетку перед Include
487    #Virtual hosts                  
488    Include etc/extra/httpd-vhosts.conf 




##2. Правим sudo gedit /opt/lampp/etc/extra/httpd-vhosts.conf

2.1. Чтобы оставить локальный хост работающим без изменений 


```XML

NameVirtualHost *:80

localhost 
<VirtualHost *:80>      
    ServerName localhost        
    DocumentRoot "/opt/lampp/htdocs"    
    <Directory "/opt/lampp/htdocs">    
        Options Indexes FollowSymLinks Includes execCGI    
        AllowOverride All  
        Allow From All    
        Order Allow,Deny   
    </Directory>
</VirtualHost>```

2.2. Подключить свой сайт — cоздать папку сайта site.local (пример) 
folder — пусть к папке сайта
site.local — папка сайта

My custom host 
<VirtualHost *:80> 
    ServerName site.local 
    DocumentRoot "/адрес до нового сайта/folder/site.local" 
    <Directory "/адрес до нового сайта/folder/site.local"> 
        Options Indexes FollowSymLinks Includes ExecCGI 
        AllowOverride All 
        Require all granted       
`</Directory>` 
ErrorLog "logs/site.local-error_log" 
`</VirtualHost>` 
 

Директиву AllowOverride None обязательно заменяем на AllowOverride All, иначе файл .htaccess не будет прочитан сервером Apache
