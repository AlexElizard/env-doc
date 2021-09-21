# Установка PostgreSQL в Arch Linux
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