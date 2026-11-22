# DNS
Описание/Пошаговая инструкция выполнения домашнего задания:
1. взять стенд https://github.com/erlong15/vagrant-bind <br><br>
•	добавить еще один сервер client2<br>
•	завести в зоне dns.lab имена:<br>
•	web1 - смотрит на клиент1<br>
•	web2  смотрит на клиент2<br>
•	завести еще одну зону newdns.lab<br>
•	завести в ней запись<br>
•	www - смотрит на обоих клиентов<br><br>

2. настроить split-dns<br><br>
•	клиент1 - видит обе зоны, но в зоне dns.lab только web1<br>
•	клиент2 видит только dns.lab<br><br>

Дополнительное задание<br>
* настроить все без выключения selinux<br><br>
______________________________________________________________

## 1.
Используем наш [Vagrantfile](https://github.com/mrigorevich/otus_dns/blob/main/Vagrantfile), отвечающий специфике хоста, добавляем в него сервер client2.<br><br>
Поднимаем стенд:<br>
`vagrant up`<br><br>
Через каталог shared скопируем ключи созданных ВМ в ansible и поставим на них права:<br>
`cp shared/*_key .ssh/`<br>
`chmod 600 .ssh/*`<br><br>
Приведем в соответствие с Ubuntu файлы [master-named.conf](https://github.com/mrigorevich/otus_dns/blob/main/master-named.conf) (здесь еще добавим уведомления slave об изменениях в зонах) и [slave-named.conf](https://github.com/mrigorevich/otus_dns/blob/main/slave-named.conf), добавим файл named.newdns.lab.<br>
И запустим [плейбук](https://github.com/mrigorevich/otus_dns/blob/main/playbook.yml):<br>
`ansible-playbook -i hosts.ini playbook.yml`<br><br>
На ВМ ns01 добавим в файл /etc/bind/named.dns.lab записи об узлах и изменим значение serial:<br><br>
<img width="974" height="139" alt="image" src="https://github.com/user-attachments/assets/912d46cf-a6a3-44f6-94f3-52bf00ce0e74" /><br>
<img width="974" height="204" alt="image" src="https://github.com/user-attachments/assets/61ad1bcf-b92e-4f0e-8d16-cea7f833b5ee" /><br><br>
Сохраним, перезапустим сервис и проверим:<br><br>
С ВМ client:<br><br>
<img width="974" height="133" alt="image" src="https://github.com/user-attachments/assets/6743a896-ff40-4fe1-a7fe-4b56e14e2b41" /><br><br>
С ВМ client2:<br><br>
<img width="974" height="131" alt="image" src="https://github.com/user-attachments/assets/d33ce2c2-f883-4ab7-8314-e0cdd3c0d457" /><br><br>
На ВМ ns01 добавим в зону newdns.lab А-записи для www:<br><br>
<img width="974" height="603" alt="image" src="https://github.com/user-attachments/assets/c6dda34d-e222-43ed-b07d-96c43615de87" /><br><br>
Перезапустим службу и проверяем на ВМ client:<br><br>
<img width="974" height="131" alt="image" src="https://github.com/user-attachments/assets/59f3dd02-feee-40ad-bd04-da1e71cabb6f" /><br><br>
Задание 1 готово.<br><br>

## 2.
На ВМ сгенерируем два ключа с помощью tsig-keygen.<br><br>
Внесём изменения в файлы master-named.conf и slave-named.conf и запустим ansible плейбук [split.yml](https://github.com/mrigorevich/otus_dns/blob/main/split.yml)<br>
`ansible-playbook -i hosts.ini split.yml`<br><br>
Проверяем на ВМ client:<br><br>
<img width="974" height="443" alt="image" src="https://github.com/user-attachments/assets/6ec5af12-b5f5-4a51-b850-11f068c39069" /><br><br>
На ВМ client2: <br><br>
<img width="974" height="445" alt="image" src="https://github.com/user-attachments/assets/65a338bb-d4b3-4be2-893b-92e63e10aa32" /><br><br>
Задание 2 готово.
