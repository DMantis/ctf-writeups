## MCTF Admin 200 Write-Up

![Задание](http://i.imgur.com/F6sFJmS.png)

Создаем пользователей mctf-admin, mctf-dep1, mctf-dep2

```shell
useradd -G mctf-dep1,mctf-dep2 -d /home/mctf-admin mctf-admin
useradd -d /home/mctf-dep1 mctf-dep1
useradd -d /home/mctf-dep2 mctf-dep2
```

Настраиваем для каждого пользователя доступ по ssh. 

Для этого копируем публичные ключи, данные в задании, в ~/.ssh/authorized_keys для каждого из пользователей.

Теперь приступаем непосредственно к серверу. Перейдя в /var/www/:

Создаем директории dep1, dep2, файлы index.php согласно условию.  
/index.php  
/dep1/index.php  
/dep2/index.php

```shell
mkdir dep1 dep2
touch index.php dep1/index.php dep2/index.php
```
И меняем их владельца на www-data

```shell
chown www-data:www-data index.php
chown -R www-data:www-data dep1
chown -R www-data:www-data dep2
```

Для сохранения www-data:www-data пользователем и группой владельцем всех директорий и файлов в www и разграничения прав для пользователей mctf-admin, mctf-dep1, mctf-dep2 используем механизм списков контроля доступа [ACL][ACL Link]

```shell
setfacl -m g:mctf-dep1:rwx dep1
setfacl -m g:mctf-dep2:rwx dep2

setfacl -m g:mctf-dep1:rwx dep1/index.php
setfacl -m g:mctf-dep2:rwx dep2/index.php

setfacl -m u:mctf-admin:rw index.php
```

На этом моменте я думал что удовлетворил все требования задания, но скормив роботу url я получил новый пустой каталог /var/www.

Для исправления этой досадной ситуации используем [chattr][Chattr Link]

```shell
chattr +a dep1
chattr +a dep2
chattr +a index.php
chattr +a .
```

Скормив роботу url нашего сервера получаем флаг!

[ACL Link]: <https://wiki.archlinux.org/index.php/Access_Control_Lists_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)>
[Chattr Link]: <https://en.wikipedia.org/wiki/Chattr>