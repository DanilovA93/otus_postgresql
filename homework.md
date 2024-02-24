### 1. создайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере:
 <img width="1379" alt="Screenshot 2024-02-24 at 20 34 11" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/49d42b26-3bf4-4fac-84c7-11b5e2ea7bbe">

### 2. поставьте на нее PostgreSQL 15 через sudo apt
#### Скрипт:  
```zsh
sudo apt install dirmngr ca-certificates software-properties-common apt-transport-https lsb-release curl -y
curl -fSsL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | sudo tee /usr/share/keyrings/postgresql.gpg > /dev/null
echo deb [arch=amd64,arm64,ppc64el signed-by=/usr/share/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main | sudo tee /etc/apt/sources.list.d/postgresql.list
sudo apt update
sudo apt install postgresql-client-15 postgresql-15
```

### 3. проверьте что кластер запущен через sudo -u postgres pg_lsclusters
#### Скрипт:  
```zsh
sudo -u postgres pg_lsclusters
```
#### Результат:  
```zsh
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

### 4. зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
```zsh
sudo -i -u postgres
psql
create table test(c1 text);
insert into test values('1');
```

### 5. остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop:
#### Скрипт:  
```zsh
sudo -u postgres pg_ctlcluster 15 main stop
```
#### Результат:  
```zsh
15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

### 6. создайте новый диск к ВМ размером 10GB
<img width="1370" alt="Screenshot 2024-02-24 at 20 45 20" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/ac5627d8-77b9-4278-a662-874729b60a5f">

### 7. добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
<img width="1384" alt="Screenshot 2024-02-24 at 20 48 28" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/292f10b9-e6b4-479e-b275-f5a6451de969">

### 8. проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux
```zsh
sudo fdisk /dev/vdb
sudo mkfs.ext4 /dev/vdb1
sudo mkdir /mnt/vdb1
sudo mount /dev/vdb1 /mnt/vdb1
sudo chmod a+w /mnt/vdb1
```

### 9. перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)
```zsh
sudo /etc/init.d/postgresql restart
sudo -u postgres pg_ctlcluster 15 main stop
```
<img width="1173" alt="Screenshot 2024-02-24 at 21 35 15" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/3afbdb61-ceaa-4726-a800-dc52d642fae3">

### 10. сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/
!!!Папки "data" НЕТ!!!
```zsh
sudo chown -R postgres:postgres /mnt/vdb1/
```

### 11. перенесите содержимое /var/lib/postgres/15 в /mnt/data - mv /var/lib/postgresql/15 /mnt/data
```zsh
mv /var/lib/postgresql/15 /mnt/vdb1
```
### 12. попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
```zsh
sudo -u postgres pg_ctlcluster 15 main start
```
Результат:
```zsh
Error: /var/lib/postgresql/15/main is not accessible or does not exist
```

### 14. задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его:  
/etc/postgresql/15/main/postgresql.conf  
<img width="922" alt="Screenshot 2024-02-24 at 21 59 34" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/653b1471-049e-4619-8632-9ff6a528a832">

### 15. напишите что и почему поменяли:  
поменяли путь до папки main так как ранее ее перенесли  

### 16. попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start  
<img width="915" alt="Screenshot 2024-02-24 at 22 02 40" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/d8fdf498-3042-4e69-9b87-ffabcb6e5203">

### 18. зайдите через через psql и проверьте содержимое ранее созданной таблицы  
<img width="339" alt="Screenshot 2024-02-24 at 22 01 50" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/f5a6fa3b-8988-494e-b550-ad557ac66c82">
