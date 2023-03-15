# Домашнее задание к занятию "`9.2 «Zabbix. Часть 1»`" - `Корольков Денис`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например,  https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

Установите Zabbix Server с веб-интерфейсом.
Приложите скриншот авторизации в админке. Приложите текст использованных команд в GitHub.

Ответ:

Последовательность выполнения:

Заходим на сайт www.zabbix.com выбираем download – далее выбираем версию zabbix, OC куда будем устанавливать, БД, Web Server.

![screen1](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen1.png)

Далее по инструкции с сайта устанавливаем Zabbix и другие компоненты.
Install Zabbix repository

`wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo apt update`

![screen2](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen2.png)
![screen3](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen3.png)

Install Zabbix server, frontend, agent

Сперва поставим Postgresql
`sudo apt install postgresql`

![screen4](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen4.png)
![screen5](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen5.png)

`sudo apt install zabbix-server-pgsql zabbix-frontend-php php8.1-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2`
В качестве zabbix-agent2 устанавливаем версию 2, так как она более современная! По инструкции с сайта берется zabbix-agent по умолчанию.

![screen6](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen6.png)
![screen7](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen7.png)

Create initial database

`sudo -u postgres createuser --pwprompt zabbix`
заходим из под привилегированного пользователя или root, переключаемся под пользователя postgres и выполняем команду createuser --pwprompt zabbix
задаем пароль 123456789

![screen8](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen8.png)

`sudo -u postgres createdb -O zabbix zabbix`
переключимся под пользователя postgres и создаем БД (-O – owner «владелец» zabbix) и zabbix (это БД)

![screen9](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen9.png)

Пишет, что не хватает прав, но я так понял это норм в этом случае и все создалось.
Теперь загрузим в эту БД скрипт, который мы раннее закачали
`zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix`

![screen10](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen10.png)

Теперь откроем файл /etc/zabbix/zabbix_server.conf

![screen11](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen11.png)

`sudo nano /etc/zabbix/zabbix_server.conf`
разкомментируем значение DBPassword и зададим его:
`DBPassword=123456789`

![screen12](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen12.png)

На заметку, настройка местоположения БД, если она находится на другом сервере
DBHost=localhost (меняем на наш путь к БД на ином сервере)

![screen13](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen13.png)

Перезапустим демона zabbix-server.service и проверим его работу:
`systemctl restart zabbix-server.service
systemctl status zabbix-server.service`

![screen14](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen14.png)

Посмотреть логи zabbix-server можно тут:
`cat /var/log/zabbix/zabbix_server.log`

![screen15.0](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen15.0.png)

Configure PHP for Zabbix frontend (Настройка PHP для веб-интерфейса)

Идем в `/etc/zabbix/nginx.conf`
Раскомментируем параметры и назовем наш сервер
`listen 8080;
server_name zabbix-dendz.lan;`	(.lan – как зона для наших нужд, вместо .local)

![screen15](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen15.png)

Теперь перезапустим демона nginx и проверим его запуск:
`systemctl restart nginx.service
systemctl status nginx.service`

![screen16](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen16.png)

Чтобы зарезолвить нашу вебку, добавим в hosts () следующую запись:
192.168.43.99		zabbix-dendz.lan

![screen17](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen17.png)
![screen18](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen18.png)
Попингуем
![screen19](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen19.png)

Посмотрим, что получилось. Зашли в настройку веб-гуйя
По имени:

![screen20](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen20.png)
По IP:
![screen21](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen21.png)
![screen22](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen22.png)
![screen23](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen23.png)

Адрес у нас «Localhost» – так как postgreSQl находится на том же сервере, что и zabbix
Назову свой сервер - zabbix15032023, часовой пояс UTC+12:00

![screen24](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen24.png)
![screen25](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen25.png)
![screen26](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen26.png)
![screen27](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen27.png)
Admin: zabbix
![screen28](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen28.png)

Сюда добавляем хосты, снабженные zabbix-agent-ами

![screen29](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen29.png)

Установленный nginx тоже рабочий:

![screen30](https://github.com/KorolkovDenis/Zabbix/blob/main/Screenshots/z1/screen30.png)

---

### Задание 2

Установите Zabbix Agent на два хоста.
Приложите скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу. Приложите скриншот лога zabbix agent, где видно, что он работает с сервером. Приложите скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные. Приложите текст использованных команд в GitHub.

Ответ:


Последовательность выполнения:


![screen11](https://github.com/KorolkovDenis/Zabbix-dz1/blob/main/Screen/screen11.png)

Далее по инструкции с сайта устанавливаем Zabbix и другие компоненты.
Install Zabbix repository

wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo apt update









---
## Дополнительные задания (со звездочкой*)

Эти задания дополнительные (не обязательные к выполнению) и никак не повлияют на получение вами зачета по этому домашнему заданию. Вы можете их выполнить, если хотите глубже и/или шире разобраться в материале.

### Задание 3*

Установите Zabbix Agent на Windows (компьютер) и подключите его к серверу Zabbix.
Приложите скриншот раздела Latest Data, где видно свободное место на диске C:

Ответ:




## Ссылка на мою работу в Google:

[Моя работа по Zabbix](https://docs.google.com/document/d/120XmYCCDS3ey-uvLxozV6fc1gb9VOKCw/edit?usp=share_link&ouid=104113173630640462528&rtpof=true&sd=true)
