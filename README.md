# Split-DNS

## Задание выполнялось с использованием VirtualBox 6.1.40, использовался vagrant с box-образом generic/debian11

## Задание:
- Добавить еще один сервер client2  
- Завести в зоне dns.lab имена web1 - смотрит на клиент1 и web2 смотрит на клиент2  
- Завести еще одну зону newdns.lab завести в ней запись www - смотрит на обоих клиентов  
- Настроить split-dns. Клиент1 - видит обе зоны, но в зоне dns.lab только web1. Клиент2 видит только dns.lab  

## Решение  
### Добавить еще один сервер client2  
Создание ВМ `client2` добавлено в файл **Vagrant**.  

### Завести в зоне dns.lab имена web1 - смотрит на клиент1 и web2 смотрит на клиент2  
Соответствующие записи (их шаблоны) добавлены в jinja2-шаблон зоны `dns.lab` (**db.dns.lab.j2**). Сгенерированные файлы зон размещаются (если это необходимо) в директории **/etc/bind** на DNS-сервреах.  

### Завести еще одну зону newdns.lab завести в ней запись www - смотрит на обоих клиентов
Добавлен jinja2-шаблон зоны `newdns.lab` (**db.newdns.lab.j2**). Сгенерированные файлы зон размещаются (если это необходимо) в директории **/etc/bind** на DNS-сервреах. 

### Настроить split-dns. Клиент1 - видит обе зоны, но в зоне dns.lab только web1. Клиент2 видит только dns.lab
Создан шаблон **named.conf.local.j2** на основании которго создаются конфигурационные файлы которые позволяют продемонстрировать работу DNS-сервера (Primary и Secondary) в режиме Split-DNS. для этого в полученных конфигурационных файлах используется опция настройки **bind** - `view`.

### Файлы:
- **Vagrant** - файл для создания ВМ и запуска ansible
- **dnssrv.yml** - ansible playbook осуществляющий настройку как DNS-серверов, так и клиентов
- **db.dns.lab.j2** - шаблон зоны `dns.lab`. На его основе создается как "полная зона", так и зона для `client1` (т.е. без записи для `web2`)
- **db.newdns.lab.j2** - шаблон зоны `newdns.lab`
- **named.conf.local.j2** - шаблон на базе которого создается конфигурационный файл **named.conf.local** на `ns01` и `ns02`.

### Замечания
- Разбиение единого конфигурационного файла **bind** (**named.conf**) на ряд файлов (**named.conf.local**, **named.conf.options** и **named.conf.default-zone**) - это специфика **Debian**
- Т.к. на клиентах в файлах **/etc/resolv.conf** не добавлены строки `search dns.lab` и `search newdns.lab`, то необходимо использовать fqdn-имена хостов для обращения к ним (т.е. `ping web` не будет работать, а `ping web.newdns.lab` будет)
- Т.к. на клиентах  в файлах **/etc/resolv.conf**  не удаляются строки добавляемые **vagrant** и указывающие на "сторонние" DNS-сервера, именя всеравно могут быть разрешены в IP, но это будут адреса не из сети `192.168.10.0/24`
