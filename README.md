# Лабораторная работа №1. Виртуальный сервер

## Студент
**Соколов Даниил, группа I2302**  
**Дата выполнения: _09.02.2025_**  

## Описание задачи
Данная лабораторная работа знакомит с виртуализацией операционных систем и настройкой виртуального HTTP сервера. В ходе работы будет установлена виртуальная машина с Debian, развернут стек LAMP, установлены PhpMyAdmin и CMS Drupal, а также выполнена настройка виртуальных хостов Apache.

## Выполнение работы

### Подготовка
1. Скачан дистрибутив **Debian (x64, без графического интерфейса)**.
2. Установлен гипервизор **QEMU**.
3. Переименован загруженный ISO-образ в `debian.iso`.

### Создание структуры папок и образа диска
1. Создана папка `lab01`.
2. В папке `lab01` создана папка `dvd` и файл `readme.md`.
3. ISO-образ Debian перемещен в `lab01/dvd`.
4. В консоли выполнена команда для создания образа диска:
   ```sh
   qemu-img create -f qcow2 debian.qcow2 8G
   ```
5. Для ознакомления с параметрами утилиты выполнена команда:
   ```sh
   qemu-img --help
   ```

### Установка ОС Debian на виртуальную машину
1. В консоли выполнена команда:
   ```sh
   qemu-system-x86_64 -hda debian.qcow2 -cdrom dvd/debian.iso -boot d -m 2G
   ```
2. Установлена ОС Debian с параметрами:
   - Имя компьютера: **debian**
   - Хостовое имя: **debian.localhost**
   - Имя пользователя: **user**
   - Пароль пользователя: **password**

### Запуск виртуальной машины
Перезагрузка виртуальной машины выполнена с командой:
```sh
qemu-system-x86_64 -hda debian.qcow2 -m 2G -smp 2 \
    -device e1000,netdev=net0 -netdev user,id=net0,hostfwd=tcp::1080-:80,hostfwd=tcp::1022-:22
```

### Установка LAMP
1. Переключение на суперпользователя:
   ```sh
   su
   ```
2. Обновление системы и установка пакетов:
   ```sh
   apt update -y
   apt install -y apache2 php libapache2-mod-php php-mysql mariadb-server mariadb-client unzip
   ```

### Установка PhpMyAdmin и CMS Drupal
1. Скачивание файлов:
   ```sh
   wget https://files.phpmyadmin.net/phpMyAdmin/5.2.2/phpMyAdmin-5.2.2-all-languages.zip
   wget https://ftp.drupal.org/files/projects/drupal-11.1.1.zip
   ```
2. Проверка наличия файлов:
   ```sh
   ls -l
   ```
3. Распаковка и перемещение файлов:
   ```sh
   mkdir /var/www
   unzip phpMyAdmin-5.2.2-all-languages.zip
   mv phpMyAdmin-5.2.2-all-languages /var/www/phpmyadmin
   unzip drupal-11.1.1.zip
   mv drupal-11.1.1 /var/www/drupal
   ```

### Настройка базы данных
```sh
mysql -u root
CREATE DATABASE drupal_db;
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON drupal_db.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Настройка виртуальных хостов Apache
1. Созданы файлы конфигурации:
   - `/etc/apache2/sites-available/01-phpmyadmin.conf`
   - `/etc/apache2/sites-available/02-drupal.conf`
2. Регистрация конфигураций:
   ```sh
   /usr/sbin/a2ensite 01-phpmyadmin
   /usr/sbin/a2ensite 02-drupal
   ```
3. Добавлены записи в `/etc/hosts`:
   ```sh
   127.0.0.1 phpmyadmin.localhost
   127.0.0.1 drupal.localhost
   ```

### Запуск и тестирование
1. Проверка версии системы:
   ```sh
   uname -a
   ```
2. Перезапуск Apache:
   ```sh
   systemctl restart apache2
   ```
3. Проверка доступности сайтов в браузере:
   - `http://drupal.localhost:1080`
   - `http://phpmyadmin.localhost:1080`

## Ответы на вопросы
1. **Как скачать файл с помощью wget?**
   ```sh
   wget <URL>
   ```
2. **Зачем каждому сайту нужна своя база и пользователь?**  
   Это повышает безопасность и изолирует данные.
3. **Как сменить порт MySQL на 1234?**  
   Изменить параметр `port = 1234` в файле `/etc/mysql/my.cnf` и перезапустить сервер.
4. **Преимущества виртуализации?**  
   Изолированность, экономия ресурсов, удобство тестирования.
5. **Зачем настраивать временную зону?**  
   Для корректности логов и планирования задач.
6. **Размер установленной ОС?**  
   Определяется командой `du -sh /`.
7. **Рекомендации по разбиению диска?**  
   Использовать отдельные разделы для `/`, `/home`, `/var`, `/tmp`.

## Вывод
В ходе работы были освоены базовые принципы виртуализации, установка ОС, настройка веб-сервера и базы данных, развертывание CMS Drupal и PhpMyAdmin, а также базовая конфигурация виртуальных хостов Apache.

## Архивирование отчета
Файлы запакованы в архив `Sokolov-Daniil-lab01.md.zip` для сдачи в Moodle.

