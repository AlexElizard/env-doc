# Установка и настройка Kerberos
## Установка
* Измените часовой пояс на часовой пояс сервера AD
```
sudo ln -sf /usr/share/zoneinfo/Asia/Vladivostok /etc/localtime 
```
* Проверьте, что время задано верно, при необходимости скорректируйте его
```
date
sudo date --set "hh:mm"c
```
* Установите kerberos. Задавать реалм не нужно
```
##########
# Debian #
##########
sudo apt install krb5-user libkrb5-dev
```
## Настройка
* Создайте/Перезапишите файл конфигурации kerberos
```
# /etc/krb5.conf

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log
 
[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 default_realm = DOMAIN.LOCAL
 default_ccache_name = KEYRING:persistent:%{uid}

[realms]
 DOMAIN.LOCAL = {
  kdc = domain.local
  admin_server = domain.local
 }

[domain_realm]
 .example.com = DOMAIN.LOCAL
 example.com = DOMAIN.LOCAL
```
* Создайте [keytab-файл](../keytab_create.md)
## Создание и проверка keytab-файла
* Создайте keytab-файл на стороне Active Directory
```
ktpass
 -princ HTTP/example.com@DOMAIN.LOCAL
 -mapuser user@domain 
 -pass password
 -crypto All
 -ptype KRB5_NT_PRINCIPAL 
 -out C:\keytabs\krb5.keytab
```
* Сохраните полученный keytab-файл и передайте его на сервер
* Добавьте ему права на чтение
```
sudo chmod a+r /etc/krb5_keytabs/krb5.keytab
```
* Проверьте работоспособность kerberos-аутентификации. Должна появиться надпись
"***Authenticated to Kerberos v5***"
```
kinit -V -k -t /etc/krb5.keytab HTTP/example.com@DOMAIN.LOCAL
```
**Примечание:** в случае неуспеха проверьте домены keytab-файла командой
```
klist -K -e -t -k /etc/krb5_keytabs/krb5.keytab
```
* Удалите полученный тикет
```
kdestroy
```
## Многодоменная аутентификация
* Создайте по keytab-файлу для каждого домена
* Объедините их командой
```
sudo ktutil

read_kt domain1.keytab
read_kt domain2.keytab
write_kt /etc/krb5_keytabs/krb5_multidomain.keytab
quit
```
* Перезапишите файл настроек kerberos
```
# /etc/krb5.conf

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 # default_realm = DOMAIN.TEST
 # default_ccache_name = KEYRING:persistent:%{uid}

[realms]
 DOMAIN.LOCAL = {
  kdc = domain.local
  admin_server = domain.local
 }
 DOMAIN2.LOCAL = {               # append string
  kdc = domain2.local            # append string
  admin_server = domain2.local   # append string
 }                               # append string

[domain_realm]
 .example.com = DOMAIN.LOCAL
 example.com = DOMAIN.LOCAL
 .example.com = DOMAIN2.LOCAL       # append string
 example.com = DOMAIN2.LOCAL        # append string
```