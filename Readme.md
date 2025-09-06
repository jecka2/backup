### Задание 

Настроить удаленный бекап каталога /etc c сервера client при помощи borgbackup. Резервные копии должны соответствовать следующим критериям:

директория для резервных копий /var/backup. Это должна быть отдельная точка монтирования. В данном случае для демонстрации размер не принципиален, достаточно будет и 2GB;
репозиторий дле резервных копий должен быть зашифрован ключом или паролем - на ваше усмотрение;
имя бекапа должно содержать информацию о времени снятия бекапа;
глубина бекапа должна быть год, хранить можно по последней копии на конец месяца, кроме последних трех.
Последние три месяца должны содержать копии на каждый день. Т.е. должна быть правильно настроена политика удаления старых бэкапов;
резервная копия снимается каждые 5 минут. Такой частый запуск в целях демонстрации;
написан скрипт для снятия резервных копий. Скрипт запускается из соответствующей Cron джобы, либо systemd timer-а - на ваше усмотрение;
настроено логирование процесса бекапа. Для упрощения можно весь вывод перенаправлять в logger с соответствующим тегом. Если настроите не в syslog, то обязательна ротация логов.

### Решение

#### 1.  Созданеие 2-х виртуальных  машин

Выполним  ансибл плейбук

```bash
ansible-playbook create_vm.yml --ask-vault-pass 
```

После выполненеия получим 2 виртуальные машины 
- Client - 192.168.1.104
- Serv - 192.168.1.105

#### 2. Настройка сервера ( serv )

Выполним  ансибл плейбук

```bash
ansible-playbook config_server.yml --ask-vault-pass 
```

Скрипт выполнит следующие действия
1) Установку необходимого ПО 
2) Создание пользователя borg 
3) Создание группу borg

#### 3. Настройка Клиента ( client )

Выполним  ансибл плейбук

```bash
ansible-playbook config_client.yml --ask-vault-pass 
```

Скрипт выполнит следующие действия
1) Установку необходимого ПО 
2) Создание пользователя borg 
3) Создание группу borg
4) Создание ключей ssh для шифрования 
5) Инициализация репозиториия для выполенения резевной копии 
6) Копирвание файлов для организации сервиса по автоматизированному выполенению резервных копий 
7) Запуск сервиса и таймера


#### 4. Результаты  

4.1 - ![Получение данных о созданых резервных копиях](https://raw.githubusercontent.com/jecka2/backup/refs/heads/main/screenshots/borg_backup.png)

4.2 - Проверим какие файлы находятся из определнного бэкап сета

```bash
borg@client:~$ borg list borg@192.168.1.105:/var/backup/::etc-2025-09-06_23:24:35
drwxr-xr-x root   root          0 Fri, 2025-09-05 01:20:14 etc
lrwxrwxrwx root   root         33 Mon, 2025-08-25 18:23:19 etc/localtime -> /usr/share/zoneinfo/Europe/Moscow
-rw-r--r-- root   root         11 Mon, 2024-04-22 16:04:27 etc/debian_version
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:59:36 etc/default
-rw-r--r-- root   root       2691 Tue, 2025-02-11 11:52:16 etc/default/open-iscsi
-rw-r--r-- root   root        284 Tue, 2025-08-05 15:59:31 etc/default/console-setup
-rw-r--r-- root   root       1117 Thu, 2024-02-22 15:30:23 etc/default/useradd
-rw-r--r-- root   root       1550 Tue, 2025-08-05 15:59:36 etc/default/grub
lrwxrwxrwx root   root         14 Tue, 2025-08-05 15:57:33 etc/default/locale -> ../locale.conf
-rw-r--r-- root   root        150 Tue, 2024-02-27 05:22:31 etc/default/cron
-rw-r--r-- root   root        297 Tue, 2023-12-05 18:36:45 etc/default/dbus
-rw-r--r-- root   root        152 Mon, 2023-04-24 18:32:27 etc/default/networkd-dispatcher
-rw-r--r-- root   root        150 Tue, 2025-08-05 15:57:38 etc/default/keyboard
-rw-r--r-- root   root       2062 Fri, 2024-04-12 20:09:41 etc/default/rsync
-rw-r--r-- root   root        149 Tue, 2025-07-08 17:50:50 etc/default/apport
-rw-r--r-- root   root        133 Tue, 2024-07-30 19:12:40 etc/default/ssh
-rw-r--r-- root   root        284 Tue, 2025-08-05 15:59:02 etc/default/sysstat
-rw-r--r-- root   root        854 Tue, 2025-08-05 15:59:02 etc/default/mdadm
-rw-r--r-- root   root       1897 Mon, 2024-03-11 16:18:21 etc/default/ufw
-rw-r--r-- root   root        460 Wed, 2024-06-05 16:46:49 etc/default/cryptdisks
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:59:35 etc/default/grub.d
-rw-r--r-- root   root        378 Tue, 2025-08-05 15:59:35 etc/default/grub.d/50-cloudimg-settings.cfg
-rw-r--r-- root   root        682 Fri, 2025-08-01 17:21:11 etc/default/motd-news
-rw-r--r-- root   root        363 Wed, 2022-10-12 09:13:34 etc/default/pollinate
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:58:04 etc/dpkg
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:58:00 etc/dpkg/origins
-rw-r--r-- root   root         83 Mon, 2024-04-22 16:04:27 etc/dpkg/origins/debian
-rw-r--r-- root   root        114 Mon, 2024-04-22 16:04:27 etc/dpkg/origins/ubuntu
lrwxrwxrwx root   root          6 Tue, 2025-08-05 15:57:16 etc/dpkg/origins/default -> ubuntu
-rw-r--r-- root   root        446 Wed, 2023-02-15 01:56:51 etc/dpkg/dpkg.cfg
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:59:41 etc/dpkg/dpkg.cfg.d
-rw-r--r-- root   root        274 Thu, 2024-12-05 14:53:51 etc/dpkg/dpkg.cfg.d/needrestart
-rw-r--r-- root   root         92 Mon, 2024-04-22 16:04:27 etc/host.conf
-rw-r--r-- root   root        267 Mon, 2024-04-22 16:04:27 etc/legal
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:59:11 etc/profile.d
-rw-r--r-- root   root         96 Mon, 2024-04-22 16:04:27 etc/profile.d/01-locale-fix.sh
-rw-r--r-- root   root        726 Mon, 2023-09-18 03:38:13 etc/profile.d/bash_completion.sh
-rw-r--r-- root   root       1107 Sun, 2024-03-31 08:33:52 etc/profile.d/gawk.csh
-rw-r--r-- root   root        757 Sun, 2024-03-31 08:33:52 etc/profile.d/gawk.sh
-rw-r--r-- root   root       1557 Sat, 2024-02-10 20:03:36 etc/profile.d/Z97-byobu.sh
-rw-r--r-- root   root        835 Wed, 2025-05-21 18:46:09 etc/profile.d/apps-bin-path.sh
-rwxr-xr-x root   root       3396 Wed, 2025-06-25 00:14:03 etc/profile.d/Z99-cloud-locale-test.sh
-rwxr-xr-x root   root        841 Wed, 2025-06-25 00:14:03 etc/profile.d/Z99-cloudinit-warnings.sh
drwxr-xr-x root   root          0 Tue, 2025-08-05 15:57:21 etc/skel
-rw-r--r-- root   root        220 Sun, 2024-03-31 11:41:03 etc/skel/.bash_logout
-rw-r--r-- root   root       3771 Sun, 2024-03-31 11:41:03 etc/skel/.bashrc
-rw-r--r-- root   root        807 Sun, 2024-03-31 11:41:03 etc/skel/.profile
drwxr-xr-x root   root          0 Fri, 2025-09-05 01:13:08 etc/update-motd.d
-rwxr-xr-x root   root       1220 Mon, 2024-04-22 16:04:27 etc/update-motd.d/00-header
-rwxr-xr-x root   root       1151 Mon, 2024-04-22 16:04:27 etc/update-motd.d/10-help-text
-rwxr-xr-x root   root       5023 Mon, 2024-04-22 16:04:27 etc/update-motd.d/50-motd-news
-rwxr-xr-x root   root        296 Tue, 2024-04-02 16:13:32 etc/update-motd.d/91-contract-ua-esm-status
etc
```

4.3 Произведем восстановления файла из бэкап сета

```bash
borg@client:/tmp$ borg extract borg@192.168.1.105:/var/backup/::etc-2025-09-06_23:24:35 etc/hostname
borg@client:/tmp$ ls -la
total 232
drwxrwxrwt 13 root root   4096 Sep  7 02:00 .
drwxr-xr-x 22 root root   4096 Sep  7 01:13 ..
drwxrwxrwt  2 root root   4096 Sep  7 01:10 .ICE-unix
drwxrwxrwt  2 root root   4096 Sep  7 01:10 .X11-unix
drwxrwxrwt  2 root root   4096 Sep  7 01:10 .XIM-unix
drwxrwxrwt  2 root root   4096 Sep  7 01:10 .font-unix
-rw-rw-r--  1 borg borg 181590 Sep  7 01:19 borg.txt
drwx------  2 borg borg   4096 Sep  7 02:03 etc
drwx------  2 root root   4096 Sep  7 01:10 snap-private-tmp
drwx------  3 root root   4096 Sep  7 01:13 systemd-private-47873434af5e4c038938f65126bf3bc0-ModemManager.service-paa7bd
drwx------  3 root root   4096 Sep  7 01:13 systemd-private-47873434af5e4c038938f65126bf3bc0-polkit.service-Ryj0jf
drwx------  3 root root   4096 Sep  7 01:13 systemd-private-47873434af5e4c038938f65126bf3bc0-systemd-logind.service-hMLrWM
drwx------  3 root root   4096 Sep  7 01:10 systemd-private-47873434af5e4c038938f65126bf3bc0-systemd-resolved.service-ry5lf9
drwx------  3 root root   4096 Sep  7 01:10 systemd-private-47873434af5e4c038938f65126bf3bc0-systemd-timesyncd.service-p8DEhv
borg@client:/tmp$ cd etc/
borg@client:/tmp/etc$ ls -la
total 12
drwx------  2 borg borg 4096 Sep  7 02:03 .
drwxrwxrwt 13 root root 4096 Sep  7 02:00 ..
-rw-r--r--  1 borg borg    7 Sep  5 01:11 hostname
borg@client:/tmp/etc$ cat hostname 
client

```

4.4 - Log
```bash
Sep 07 02:23:28 client borg[1236]: ------------------------------------------------------------------------------
Sep 07 02:23:28 client borg[1236]: Repository: ssh://borg@192.168.1.105/var/backup
Sep 07 02:23:28 client borg[1236]: Archive name: etc-2025-09-07_02:17:48
Sep 07 02:23:28 client borg[1236]: Archive fingerprint: 12e55cdb8388c5633cb58eae8288be2e053cdce3d90b59169f164b0d381a86ec
Sep 07 02:23:28 client borg[1236]: Time (start): Sun, 2025-09-07 02:18:26
Sep 07 02:23:28 client borg[1236]: Time (end):   Sun, 2025-09-07 02:18:27
Sep 07 02:23:28 client borg[1236]: Duration: 0.33 seconds
Sep 07 02:23:28 client borg[1236]: Number of files: 821
Sep 07 02:23:28 client borg[1236]: Utilization of max. archive size: 0%
Sep 07 02:23:28 client borg[1236]: ------------------------------------------------------------------------------
Sep 07 02:23:28 client borg[1236]:                        Original size      Compressed size    Deduplicated size
Sep 07 02:23:28 client borg[1236]: This archive:                2.30 MB            988.51 kB                584 B
Sep 07 02:23:28 client borg[1236]: All archives:               41.47 MB             17.78 MB              1.22 MB
Sep 07 02:23:28 client borg[1236]:                        Unique chunks         Total chunks
Sep 07 02:23:28 client borg[1236]: Chunk index:                     804                14617
Sep 07 02:23:28 client borg[1236]: ------------------------------------------------------------------------------
```