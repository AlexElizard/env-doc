# Установка и настройка PostgreSQL
## Установка
* [Arch Linux](install/arch.md)
* [Debian](install/debian.md)

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
* Установите PostGIS
```
##########
# Debian #
##########
sudo apt-get update && sudo apt install postgis

##############
# Arch Linux #
##############
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