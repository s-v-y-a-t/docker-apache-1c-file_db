# Запуск Apache 2.4 с модулем 1С внутри Docker контейнера

1. Установите Docker (https://docs.docker.com/install/) и docker-compose (https://docs.docker.com/compose/install/)

2. При установке Docker на Linux не забудьте про ещё один важный шаг: https://docs.docker.com/install/linux/linux-postinstall/

3. Скачайте дистрибутив 1С сервера для Linux в текущую директорию: https://releases.1c.ru -> Технологическая платформа 8.3 -> Cервер 1С:Предприятия (64-bit) для DEB-based Linux-систем - должен получиться файл `deb64.tar.gz`

4. Проверьте настройки подключения к информационной базе на сервере 1С в файле `default.vrd` (в этом репозитории)

5. Проверьте и поправьте имя информационной базы в файле `httpd.conf` в самом конце (в этом репозитории), по умолчанию там используется BuhBase. На самом деле этот шаг не обязателен, путь на веб-сервере может быть любым, но для консистентности удобно, когда он совпадает с именем информационной базы

6. Если поменяли директорию BuhBase в настройках веб-сервера (в `httpd.conf`) на какую-то свою, то не забудьте обновить и `Dockerfile` (в этом репозитории), там где происходит копирование `default.vrd` в директорию BuhBase

7. Проверьте в файле `docker-compose.yml` (в этом репозитории) маппинг портов из хост системы внутрь контейнера (по умолчанию стоит `ports: 80:80`, но если на хост машине порт 80 уже занят, можно поменять на что-то другое, например, `ports: 8000:80`) и настройте привязку имени хоста где запущен 1С сервер к его IP адресу (параметр `extra_hosts`)

8. Запустите командой `docker-compose up -d --build`

Теперь можно проверить в бразуре: `http://<ip адрес linux хоста>/BuhBase/` или  `http://<ip адрес linux хоста>:<порт на linux хосте как в docker-compose.yml>/<путь указанный в httpd.conf>/`


## При обновлении версии платформы 1C:
1. Скачать новый дистрибутив (`deb64.tar.gz`) в директорию проекта

2. Если был мажерный переход с 8.3 на 8.4, то, возможно, придётся поправить путь к `wsap24.so` в `httpd.conf`

3. Находясь в директории проекта остановить контейнер: `docker-compose down`

4. Пересобрать образ и запустить новый контейнер: `docker-compose up -d --build`

## Если нужно добавить несколько информационных баз:
1. Понадобится несколько .vrd файлов - каждую базу свой .vrd

2. Добавить инструкции по их копированию внутрь контейнера в `Dockerfile`

3. Описать пути для веб-сервера в `httpd.conf`: на каждую базу свой путь, своя директория, своя ссылка на соответсвующий .vrd

4. Находясь в директории проекта остановить контейнер: `docker-compose down`

5. Пересобрать образ и запустить новый контейнер: `docker-compose up -d --build`
