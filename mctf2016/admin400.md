## M*CTF 2016 Admin 400 Write-Up

В описании задания дан образ [виртуальной машины](https://yadi.sk/d/XC45RpSgvbpJ4) и дан намек на то, что файл флага был удален.

![Задание](http://i.imgur.com/Y2v37Q2.png)

Для просмотра содержимого виртуальной машины я подключил ее виртуальный диск и загрузился с загрузочного образа с Linux.  
Просмотрев корневую директорию, можно увидеть папку secret. К сожалению, она пуста. Но зайдя в /root можно увидеть файл .bash_history. Аллилуйя! 

Содержимое файла  .bash_history:
```shell
mkdir /secret
mount -t tmpfs -o size=100M tmpfs /secret
mount
nano /secret/flag
ls /secret/
gpg —yes —batch —passphrase="flag_is_so_close" -c /secret/flag
srm /secret/flag
sfill /secret
ls /secret/
sfill /secret/
ls /secret/
nano ~/.bash_history
sync
nano ~/.bash_history
history -r
mkdir /secret
mount -t tmpfs -o size=100M tmpfs /secret
mount
nano /secret/flag
ls /secret/
gpg —yes —batch —passphrase="flag_is_so_close" -c /secret/flag
srm /secret/flag
ls /secret/
history -h
nano ~/.bash_history
```

Из истории команд видно, что флаг был зашифрован под пароль _flag_is\_so\_close_ и затем удален.

Вместе с виртуальной машиной идет снимок состояния ее диска. Вероятно, как раз на момент выполнения этих команд. Несколько минут с hex редактором выдают заголовок PGP-зашифрованных файлов: 8c0d0403

Попробуем найти что-либо похожее в нашем снимке
```shell 
$ xxd admin400-Snapshot1.vmem | grep 8c0d | grep 0403
1698a000: 8c0d 0403 0302 d581 6f24 644e 8fbb 60c9 ........o$dN..`.
```
Отлично, что-то похожее лежит по смещению 1698a000, посмотрим:
```shell
$ xxd -s 0x1698a000 -l 100 admin400-napshot1.vmem
1698a000: 8c0d 0403 0302 d581 6f24 644e 8fbb 60c9 ........o$dN..`.
1698a010: 2f27 adfa 6999 2db7 447c 80e4 96f5 026b /'..i.-.D|.....k
1698a020: 03ac 7f4a 2467 9ef0 b908 f8f7 41f5 26f9 ...J$g......A.&.
1698a030: ef11 853c 3b13 b74a acba 3a63 e98f 1b8d ...<;..J..:c....
1698a040: 0000 0000 0000 0000 0000 0000 0000 0000 ................
1698a050: 0000 0000 0000 0000 0000 0000 0000 0000 ................
1698a060: 0000 0000
$ xxd -p -s 0x1698a000 -l 100 admin400-Snapshot1.vmem | xxd -r -p > tmp.gpg
$ gpg --decrypt --passphrase="flag_is_so_close" tmp.gpg
gpg: данные зашифрованы алгоритмом CAST5
gpg: зашифровано с 1 фразой-паролем
That_Was_Easy_Aight?
gpg: ВНИМАНИЕ: целостность сообщения не защищена
```

Флаг _That_Was_Easy_Aight_


