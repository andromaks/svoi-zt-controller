# Разворачиваем собственный Network Controller для Zerotier
Видео об этом процессе доступно по ссылке

На сайте [docs.zerotier.com ](https://docs.zerotier.com/what-is-a-controller) доступна документация по Network Controller.
Описание поддерживаемых http запросов.
Zerotier не предоставляет собственный Network Controller, который можно установить на собственной инфраструктуре.

Будем расмматривать сторонние решения.
В данном документе рассмотрим [Zero-ui](https://github.com/dec0dOS/zero-ui) 

Преимущества:
- устанавливается в Docker
- минимальные требования к инфраструктуре
- встроенная поддержка для создания https сертификатов на web интерфейсе
- удобный backup и перенос на другой сервер
- нет ограничений на количество подключаемых нод и количество создаваемых сетей.

Недостатки
- Нет полной совметсимости по сервисам с оригинальным Zerotier
- поддерживает только IPv4
- Поддердживает только одного администратора

## Что нужно для установки?
Хост для Docker. Это может быть Linux или Windows. Достаточно 1 vCPU и 1G RAM. Вполне возможно будет работать и с более скромными требованиями.
Делайте установку Docker согласно документации на [Установка Docker](https://docs.docker.com/engine/install/)


## Установка Zero-UI

1. Создаем директорию, где будет распологаться Network Controller.
</code></pre>mkdir -p /srv/zero-ui/
cd /srv/zero-ui/
</code></pre>

2. Скачиваем docker-compose.yaml

</code><pre> wget https://raw.githubusercontent.com/dec0dOS/zero-ui/main/docker-compose.yml
</code></pre>
<p>или</p> 
   
</code><pre> curl -L -O https://raw.githubusercontent.com/dec0dOS/zero-ui/main/docker-compose.yml
</code></pre>

4. Изменяем YOURDOMAIN.com на ваше доменное имя и устанавливаем пароль администратора (ZU_DEFAULT_PASSWORD) в docker-compose.yml
5. Скачиваем докер имидж
   docker pull dec0dos/zero-ui
6. Разрешаем порты на фаерволе
<p>или при помощи ufw</p> 
<pre><code>ufw allow 80/tcp
ufw allow 443/tcp
ufw allow 9993/udp
</code></pre>
<p>или при помощи iptables</p>
<pre><code>iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -p udp --dport 9993 -j ACCEPT
</code></pre>

7. Запускаем контейнер
   docker-compose up -d --no-build
8. Проверяем что все работает без ошибок (Control-C остновить просмотр логов)
   docker-compose logs -f
9. Идем на https://ваш_домен.ru/app/. Заходим в Network Сontroller login:admin пароль: то, что написано в ZU_DEFAULT_PASSWORD.

Дополнительные параметры доступны в документации на  [Zero-ui](https://github.com/dec0dOS/zero-ui) 

## Резервное копирование.
просто делаем копию двух директорий и docker-compose.yaml

<pre><code>tar cvf backup-ui.tar data/
tar cvf backup-zt.tar zerotier-one/
cp docker-compose.yaml docker-compose.yaml.backup
</code></pre>

При наличии backup-ui.tar backup-zt.tar и docker-compose.yaml.backup Network Controller переносится на любой другой docker хост.
Не забываем переделать DNS А записи при изменении хоста.

## Собственный Network Controller работает.

### Варианты настройек Zerotier сетей смотрите в видео [Виртуальные сети в Zerotier](https://www.youtube.com/watch?v=MjI1h7IJQsQ)
