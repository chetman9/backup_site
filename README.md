# backup_site
backup_site
192.168.88.50 master
192.168.88.51 slave
192.168.88.55:8080 apache1
192.168.88.56:8080 apache2
192.168.88.57 promet+grafana
192.168.88.58 nginx
192.168.88.60 backup ftp

На всех виртуальных машинах должен быть установлен centos 7
Все конфиги лежат на ftp ip 192.168.88.60, логин: back пароль: backup
1. Восстановить базу данных master mysql db_drupal 192.168.88.50, запустить скрипт master
    При запуске скрипта - master установится mysql_secure_installation, Задать пароль Qwerty123!!!
    Создать пользователя для синхронизации мастер слейва
    CREATE USER repl@'%' IDENTIFIED WITH 'caching_sha2_password' BY 'oTUSlave#2020';
    GRANT REPLICATION SLAVE ON *.* TO repl@'%';
2. Восстановить базу данных slave mysql db_drupal 192.168.88.51 запустить скрипт slave
    При запуске скрипта - master установится mysql_secure_installation, Задать пароль Qwerty123!!!
    Посмотреть бинлог и лог позицию на сервере МАСТЕР 192.168.88.50 помощью SHOW MASTER STATUS;
    CHANGE MASTER TO MASTER_HOST='192.168.88.50', MASTER_USER='repl', MASTER_PASSWORD='oTUSlave#2020', MASTER_LOG_FILE='binlog.000001', MASTER_LOG_POS=14658488, GET_MASTER_PUBLIC_KEY = 1;
    После этого ввести команду start slave;
    Посмотреть статус слэйва show master status\G;
    Добавить в крон задание 0 23 * * * /home/kostya/192.168.88.60/mysql_slave/dump
3. Заупустить севрер apache1, apache2, заупсутить скрипт имя - apache
4. запустить nginx сервер, запустить скрипт - nginx
5. Заупустить prometheus grafana
    Добавить data source http://localhost:9090
    Добавить Панель визуализации id 1860
