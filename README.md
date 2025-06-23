# Всем привет в этой репе я собрал информацию по которой я учился администрировать,

## Установка PostgreSQL 
Официальная документация <https://www.postgresql.org/download/linux/ubuntu/>

### Загрузки для Linux (Ubuntu) 
PostgreSQL доступен во всех версиях Ubuntu по умолчанию. Однако Ubuntu «создает 
моментальные снимки» определенной версии PostgreSQL, которая затем поддерживается 
на протяжении всего срока службы этой версии Ubuntu. Другие версии PostgreSQL 
доступны в репозитории apt PostgreSQL. 
### Apt-репозиторий PostgreSQL 
Если версия, включенная в вашу версию Ubuntu, не та, которую вы хотите, вы можете 
использовать репозиторий PostgreSQL Apt . Этот репозиторий будет интегрирован с 
вашими обычными системами и системой управления исправлениями, а также будет 
предоставлять автоматические обновления для всех поддерживаемых версий PostgreSQL в 
течение всего срока поддержки PostgreSQL . 
Репозиторий PostgreSQL Apt поддерживает текущие версии Ubuntu: 

* Kinetic (22.10, не LTS) 
* Focal (20.04, LTS) 
* Bionic (18.04, LTS) 
на следующих архитектурах: 
* amd64
* arm64 (18.04 и новее; только версии LTS) 
* i386 (18.04 и старше) 
* ppc64el (только версии LTS)
 
Чтобы использовать репозиторий apt, выполните следующие действия: 

# Create the file repository configuration: 
`sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'` 

# Import the repository signing key: 
`wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -` 

# Install the latest version of PostgreSQL.
# If you want a specific version, use 'postgresql-12' or similar instead of 'postgresql': 
`sudo apt-get -y install postgresql` 

Дополнительные сведения о репозитории apt, включая ответы на часто задаваемые 
вопросы, см. на странице PostgreSQL Apt Repository в вики. 

## Входит в дистрибутив
Ubuntu включает PostgreSQL по умолчанию. Чтобы установить PostgreSQL в Ubuntu,
используйте команду apt-get (или другую команду apt):
`apt-get install postgresql-12` 
  
Репозиторий содержит множество различных пакетов, включая сторонние 
дополнения. Наиболее распространенные и важные пакеты (при необходимости замените номер версии):

|Версия пакета|Содержимое пакетов|
|-|--------|
postgresql-client-12|клиентские библиотеки и клиентские двоичные файлы 
postgresql-12|основной сервер базы данных
libpq-dev|библиотеки и заголовочные файлы для разработки интерфейса на языке C
postgresql-server-dev-12|библиотеки и заголовочные файлы для разработки бэкенда на языке C

Убедитесь, что служба запущена: 
`$ sudo systemctl start postgresql.service`

Проверка статуса сервиса: 
`$ sudo systemctl status postgresql.service`

## Работа с аккаунтом PostgreSQL 
PostgreSQL применяет термин «Роль». Практически это тот же аккаунт в Ubuntu. При 
запуске СУБД роли сервиса привязываются к одноименным аккаунтам в Unix-системах. 
Другими словами, при наличии роли в PostgreSQL, войти в СУБД можно с аккаунтом 
Ubuntu. При запуске СУБД генерируется аккаунт postgres, привязываемый к роли 
PostgreSQL.

### Вариант 1

Войдем в аккаунт: 
`$ sudo -i -u postgres`  
После ввода команды видим подтверждение о переходе в аккаунт: 
`postgres@postgresdoc:~$` 
Откроем консоль Postgres: 
`$ psql`
Консоль открыта, что подтверждается записью в начале строки: 
`postgres=#` 
Работа в СУБД ведется из консоли. 
Узнать статус подключения: 
`postgres=# \conninfo` 
Возврат в аккаунт: 
`postgres=# \q`  
  
### Вариант 2 
Войдем в аккаунт postgres с sudo. Если сейчас находимся в аккаунте postgres, нужно 
выйти, набрав exit. В этом варианте перейдем в аккаунт postgres с sudo: 
`$ sudo -u postgres psql` 
Возврат в аккаунт: 
`postgres=# \q`

## Создание роли 
Аккаунт postgres обладает правами администратора. Напишем createuser, эта команда 
сообщает, что мы добавляем новую роль. Чтобы указать имя роли и выдать суперюзера, 
применим флаг —interactive. 
Запись будет такой: 
`postgres@postgresdoc:~$ createuser --interactive`  
Вариант работы без переходов между аккаунтами: 
`$ sudo -u postgres createuser --interactive`  
Вводим имя, выдаем суперюзера: 
`Enter name of role to add: tester 
Shall the new role be a superuser? (y/n) y`  
Посмотреть другие ключи настроек: 
`postgres@postgresdoc:~$ man createuser`
Роль создана, поднимаем БД. 

## Создание базы данных 
Любому созданному аккаунту привязывается база данных с идентичным именем, то есть 
наш созданный tester начнет подключаться к базе данных tester. 
Командой createdb добавим БД (поднимем новую базу PostgreSQL на Ubuntu), назвав её tester: 
`postgres@postgresdoc:~$ createdb tester`  
Вариант работы без переходов между аккаунтами: 
`$ sudo -u postgres createdb tester`
Переход в командную строку PostgreSQL с новой ролью 
Работа в консоли PostgreSQL подразумевает наличие аккаунта Ubuntu с именем БД 
в Postgres. 
Добавим аккаунт Ubuntu, используя adduser (предварительно выйдя из аккаунта postgres), 
назвав аналогично новой роли: 
  
$ sudo adduser tester 
Добавив аккаунт tester, переключаемся на него и подключаемся к консоли: 
$ sudo -i -u tester 
$ psql  
второй вариант: 
$ sudo -u tester psql  
Переключиться на другую БД: 
$ psql -d postgres 
Проверка статуса: 
tester=# \conninfo  
Увидим: 
You are connected to database " tester " as user " tester " via socket 
in "/var/run/postgresql" at port "5432". 
Проверку желательно выполнять для разных пользователей с разными БД. 
Создание таблицы с данными 
Команда создания имеет вид: 
CREATE TABLE table_name ( 
column_name1 col_type (field_length) column_constraints, 
column_name2 col_type (field_length), 
column_name3 col_type (field_length) 
);  
Создавая таблицу, указываем ее имя, столбцы, их типы, ограничения размеров полей. 
В качестве непосредственного примера добавим таблицу комплекта сноубордиста: 
tester=# CREATE TABLE snowboarder (  
equip_id serial PRIMARY KEY,  
title varchar (50) NOT NULL,  
company varchar (25) NOT NULL,  
size varchar (25) check (size in ('XS', 'S', 'M', 'L', 'XL', 
'XXL'))  
); 
СУБД выводит информацию: 
CREATE TABLE 
Методические рекомендации по проведению итоговой аттестации DevOps-инженер с нуля (v1.0.4) 
Страница 10 из 79 
ПАО Ростелеком  
 
 
 
Equip_id — столбец с идентификатором типа serial и автоинкрементом. Ему 
присвоили свойство primary key, указывающее на использование не нулевых 
показателей. 
Title и company указывают наименование и фирму-производителя единицы 
комплекта. 
Size хранит размеры наименований комплекта, предлагая выбор одного 
из предложенных размеров. 
Посмотреть таблицу: 
tester=# \d 
Вывод: 
List of relations  
Schema |          
Name            
|    
Type  | Owner  -------+--------------------------+----------+--------  
public | snowboarder              
| table    
| tester  
public | snowboarder_equip_id_seq | sequence |  
tester (2 rows)  
Создана таблица с переменной snowboarder_equip_id_seq, тип данных sequence. 
Переменная указывает на номера последовательности и генерируется автоматически. 
Вывести таблицу без переменной: 
tester=# \dt  
Вывод: 
List of relations  
Schema |     
Name    
| Type  | Owner  -------+-------------+-------+---------  
public | snowboarder | table | tester  
(1 rows)  
Работа с данными таблицы 
Таблица есть, остается ее наполнить. 
Добавление данных в таблицу 
Состав комплекта нашего сноубордиста: snowboard (сноуборд), binding (крепления), boots 
(ботинки). Вызовем таблицу, где укажем столбцы и их значения. Добавим 3 строки: 
tester=# INSERT INTO snowboarder (title, company, size) VALUES 
('snowboard', 'burton', 'XL');  
tester=# INSERT INTO snowboarder (title, company, size) VALUES 
('binding', 'burton', 'XL');  
tester=# INSERT INTO snowboarder (title, company, size) VALUES 
('boots', 'burton', 'XL');  
Пишем внимательно, избегая ошибок. Неверно использовать кавычки в названиях 
столбцов. Кавычки указываются в значениях столбцов. Значение столбца equip_id 
генерируется самостоятельно во время создания строки. 
После добавления каждой строки СУБД подтверждает операцию: 
Методические рекомендации по проведению итоговой аттестации DevOps-инженер с нуля (v1.0.4) 
Страница 11 из 79 
ПАО Ростелеком  
INSERT 0 1 
Вывод данных  
Выведем таблицу с новыми данными: 
tester=# SELECT * FROM snowboarder;  
Увидим в ответ: 
equip_id |   title   | company | size  ---------+-----------+---------+------  
1   
| snowboard | burton  | XL  
2   
| binding   | burton  | XL  
3   
| boots     
| burton  | XL  
(3 rows)  
Видим, что все поля заполнены значениями, прописанными нами, 
и в equip_id отображается нумерация строк. 
