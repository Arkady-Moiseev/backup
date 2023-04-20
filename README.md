# BACKUP
___
<b>Настроить стенд Vagrant с двумя виртуальными машинами: backup_server и client</b><br>
<br><b>Настроить удаленный бэкап каталога /etc c сервера client при помощи borgbackup</b><br>
___

Подготавливаем стенд -> <a href="https://github.com/Arkady1996/backup/blob/main/Vagrantfile">Vagrantfile</a>

После создания VM необходимо создать раздел на диске и создать файловую систему (в дальнейшем необходимо данный раздел примонтироавть к директории <b>/var/backup</b>). 

Устанавливаем borgbackup  "<b>yum install epel-release && yum install borgbackup</b>." Создаем раздел для бекапов, пользователя и предоставляем права "useradd borg && mkdir /var/backup && chown borg:borg /var/backup/".

Генерируем на клиете <b>SSH</b> ключ и экспортируем его на сервер для бекапов.


![img_1](https://github.com/Arkady1996/backup/blob/main/images/1.png)


Инициализируем репозиторий borg на backup сервере с client сервера:<br>
"<b>borg init --encryption=repokey borg@192.168.50.10:/var/backup/</b>"<br>
и создаем тестовый бекап:<br>
"<b>borg create --stats --list borg@192.168.50.10:/var/backup/::"etc-{now:%Y-%m-%d_%H:%M:%S}" /etc</b>"<br>
и проверяем:<br>
"<b>borg list borg@192.168.11.160:/var/backup/</b>"


![img_2](https://github.com/Arkady1996/backup/blob/main/images/2.png)

![img_3](https://github.com/Arkady1996/backup/blob/main/images/3.png)

Проверяем содержимое бекапа:<br>
"<b>borg list borg@192.168.50.10:/var/backup/::etc-2023-04-19_19:18:04</b>"

![img_4](https://github.com/Arkady1996/backup/blob/main/images/4.png)

# Автоматизируем создание бэкапов с помощью systemd<br>
Создаем <a href="https://github.com/Arkady1996/backup/blob/main/borg-backup.service">сервис</a> и <a href="https://github.com/Arkady1996/backup/blob/main/borg-backup.timer">таймер</a> в каталоге /etc/systemd/system/.<br>
Включаем запускаем службу таймера ипроверяем работоспособность:<br>
<b>systemctl enable borg-backup.timer</b> <br>
<b>systemctl start borg-backup.timer</b>

Проверяем работу сервиса и таймера


![img_5](https://github.com/Arkady1996/backup/blob/main/images/5.png)

![img_6](https://github.com/Arkady1996/backup/blob/main/images/6.png)

