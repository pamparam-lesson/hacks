## Install Git
обновляем 

`sudo apt-get update`   
устанавливаем
`sudo apt-get install git`   

## Install JDK
Удаляем старые версии OpenJDK

`sudo apt-get remove openjdk*`   
устанвливаем новый репзиторий oracle jdk

`sudo add-apt-repository ppa:webupd8team/java`  

обновляем компоненты   

`sudo apt-get update`  

устанавливаем oracle-java8  

`sudo apt-get install java-common oracle-java8-installer`  
В процессе установки вам необходимо будет принять условия лицензионного соглашения Oracle. 
После установки мы должны установить переменные окружения Java, такие как JAVA_HOME 
`sudo apt-get install oracle-java8-set-default`  

`source /etc/profile`   


## Install PhpStorm

Качаем idea с [оф. сайта Phphstorm](https://www.jetbrains.com/phpstorm/download/#section=linux-version)

Затем преходим в папку куда скачали  PhpStorm вызываем Termenal ctl+alt+T
 
 и разархивируем с помощью команды
 
 `tar xvf PhpStorm-2016.1.2.tar.gz`    
 
 затем переносим все файлы в папку /opt (либо какую захотите) 
 для этого нужно набрать команду  
 `sudo mv PhpStorm-145.1616.3/ /opt/phpstorm/`    
  
 делаем ссылку      
 
 `sudo ln -s /opt/phpstorm/bin/phpstorm.sh /usr/local/bin/phpstorm`  
 
 Теперь что бы запустить можно просто набрать в терменале:
 
 `phpstorm`      
 
 Если потребуется удалить то набираем комманду:
 
 `sudo rm /opt/phpstorm/ -R`   