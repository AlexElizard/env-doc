    # Установка и настройка PostgreSQL
## Установка через репозиторий
### Debian
* Подключите apt-репозиторий
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```
* Скачайте ключ проверки для apt
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
* Установите PostgreSQL
```
sudo apt-get update && sudo apt install postgresql
```
### Arch
* Установите PostgreSQL
```
sudo pacman -Suy postgresql
```
* Инициализируйте кластер базы данных
```
sudo su postgres
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data/'
exit
```
* Запустите PostgreSQL-сервер и добавьте его запуск в автозагрузку
```
sudo systemctl enable --now postgresql
```
## Создание базы данных
* Подключитесь к БД
```
sudo su postgres
psql
```
* Создайте БД, где `dbname` - имя создаваемой базы
```
CREATE DATABASE dbname;
```
* Создайте пользователя-владельца БД, где `dbuser` - имя пользователя, `dbpasswd` - пароль пользователя
```
CREATE USER dbuser PASSWORD 'dbpasswd';
```
* Предоставьте пользователю права доступа
```
GRANT ALL PRIVILEGES ON DATABASE dbname TO dbuser;
```
* Отключитесь от PostgreSQL и вернитесь на своего пользователя
```
exit
exit
```
# Расширения (Опционально)
## PostGIS (для работы с Geo-данными)
###  Установка
#### Debian
* Установите PostGIS
```
sudo apt-get update && sudo apt install postgis
```
#### Arch
* Установите PostGIS
```
sudo pacman -Suy postgis
```
### Включение расширения
* Подключитесь к своей БД
```
sudo su postgres
psql -d dbname -U dbuser -h 127.0.0.1
```
* Включите расширение
```
CREATE EXTENSION postgis;
```
* Отключитесь от PostgreSQL и вернитесь на своего пользователя
```
exit
exit
```