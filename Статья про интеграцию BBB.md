<!-- title: Статья про интеграцию BigBlueButton, Keyсloak, FreeIPA -->
<!-- Дата создания документа 26.06.2025 -->
<!-- omit from toc -->
# Статья про интеграцию BigBlueButton, Keyсloak, FreeIPA

<!-- omit from toc -->
## Содержание

- [Для кого статья](#dlia-kogo-stat-ia)
- [Что за серверы `BigBlueButton`, `Keyсloak`, `FreeIPA`?](#chto-za-servery-bigbluebutton-keysloak-freeipa)
- [О чём будет идти речь в статье?](#o-chiom-budet-idti-rech-v-stat-e)
- [Что не будет рассматриваться в статье](#chto-ne-budet-rassmatrivat-sia-v-stat-e)
- [Как установить `BigBlueButton`](#kak-ustanovit-bigbluebutton)
- [Что устанавливается](#chto-ustanavlivaetsia)
- [Куда устанавливается `BigBlueButton`](#kuda-ustanavlivaetsia-bigbluebutton)
- [Где располагается сервер `BigBlueButton`](#gde-raspolagaetsia-server-bigbluebutton)
- [Порты TCP и UDP для работы `BigBlueButton`](#porty-tcp-i-udp-dlia-raboty-bigbluebutton)
- [Порты `TCP` и `UDP` для работы `Keycloak` для подключения к `FreeIPA`](#porty-tcp-i-udp-dlia-raboty-keycloak-dlia-podkliucheniia-k-freeipa)
- [Устанавливаем `BigBlueButton` на сервер `Ubuntu`](#ustanavlivaem-bigbluebutton-na-server-ubuntu)
  - [Ключи установки](#kliuchi-ustanovki)
- [Что получится в результате запуска скрипта `bbb-install.sh`](#chto-poluchitsia-v-rezul-tate-zapuska-skripta-bbb-install-sh)
- [Создание записи администратора для `BigBlueButton` (и почему это можно не делать)](#sozdanie-zapisi-administratora-dlia-bigbluebutton-i-pochemu-eto-mozhno-ne-delat)
- [Сервер `BigBlueButton` готов](#server-bigbluebutton-gotov)
- [Настраиваем `Keycloak`](#nastraivaem-keycloak)
  - [Следующий шаг, создание клиента](#sleduiushchii-shag-sozdanie-klienta)
- [Настраиваем подключение `Keycloak` к `FreeIPA`](#nastraivaem-podkliuchenie-keycloak-k-freeipa)
  - [Добавляем отображение поля `uidNumber`](#dobavliaem-otobrazhenie-polia-uidnumber)
  - [Добавляем поиск групп](#dobavliaem-poisk-grupp)
  - [Проверим поля `firstName`, `lastName`](#proverim-polia-firstname-lastname)
    - [Отображение `first Name`](#otobrazhenie-first-name)
  - [Отображение `lastName`](#otobrazhenie-lastname)
- [Синхронизация `Keycloak` и `FreeIPA`](#sinkhronizatsiia-keycloak-i-freeipa)
- [Просмотр пользователей](#prosmotr-pol-zovatelei)
- [Настройка сопоставления групп](#nastroika-sopostavleniia-grupp)
- [Подключаем `Keycloak` к `Greenlight`](#podkliuchaem-keycloak-k-greenlight)
- [Как сделать](#kak-sdelat)
  - [Как присвоить роль администратора для учетной записи в `Keycloak`](#kak-prisvoit-rol-administratora-dlia-uchetnoi-zapisi-v-keycloak)
- [Проблемы](#problemy)
  - [При изменении в `User federation` и обновлении информации о пользователях появляется ошибка отображения](#pri-izmenenii-v-user-federation-i-obnovlenii-informatsii-o-pol-zovateliakh-poiavliaetsia-oshibka-otobrazheniia)
  - [При сопоставлении групп появляется ошибка](#pri-sopostavlenii-grupp-poiavliaetsia-oshibka)
- [Основные команды, которые могут Вам потребоваться при работе через SSH-клиент](#osnovnye-komandy-kotorye-mogut-vam-potrebovat-sia-pri-rabote-cherez-ssh-klient)
  - [Файлы и директории, которые могут быть полезны](#faily-i-direktorii-kotorye-mogut-byt-polezny)
- [Дополнительные материалы](#dopolnitel-nye-materialy)


## Для кого статья

Статья для тех, кому нужен сервер `BigBlueButton` и нужно что бы он брал учётные записи из сервера `FreeIPA`, но не знают как подступиться и ищут информацию.

В `Инете` полно статей о настройке `BigBlueButton`, но в большинстве статьи устаревшие. К установке `BigBlueButton` версии 3.0 не годятся. Потратил 2 или 3 недели на поиски руководств и попыток установить `BigBlueButton`.

Вот об этом и пишу, опираясь на свой опыт.

## Что за серверы `BigBlueButton`, `Keyсloak`, `FreeIPA`?

`BigBlueButton` - Открытое программное обеспечение, сервер онлайн конференций.

`Keyсloak` - Открытое программное обеспечение, служба аутентификации для входа в приложения. Еще называется "Единый вход в систему" - `SSO` - `"Single-Sign On"`.

`FreeIPA` - Открытое программное обеспечение, служба каталогов, управление аутентификацией пользователей.

## О чём будет идти речь в статье?

Быстро будет рассмотрена установка `BigBlueButton`. Всё в общем расписано на странице руководства по установке `BigBlueButton` [Install BigBlueButton](<https://docs.bigbluebutton.org/administration/install/>).

Подробно будет разбираться настройка службы `Keycloak` для подключения этой службы к серверу `FreeIPA`.

## Что не будет рассматриваться в статье

В статье не будет рассматриваться настройка сервера `FreeIPA`.

Рассматриваются опции подключения `Keycloak` только в рамках подключения к `FreeIPA`.

## Как установить `BigBlueButton`

Тут всё просто. Нужно читать [Install BigBlueButton](<https://docs.bigbluebutton.org/administration/install/>) и всё сделать как там написано.

## Что устанавливается

`BigBlueButton` версии 3.0.

## Куда устанавливается `BigBlueButton`

Устанавливается на сервер `Ubuntu` версии 22.04.

## Где располагается сервер `BigBlueButton`

Сервер `BigBlueButton` расположен за NAT, в локальной сети.

## Порты TCP и UDP для работы `BigBlueButton`

Для работы `BigBlueButton` на сервере `BigBlueButton` и шлюзе открыты порты:

```
TCP: 80, 443
UDP: 16384 - 32768
```

Да, больше никаких портов открытых для `BigBlueButton` не нужно. Всё будет работать.

[Замечание]
Нужно проверить открытые порты до установки `BigBlueButton`.

Почитать можно здесь: [Firewall Configuration](<https://docs.bigbluebutton.org/administration/firewall-configuration/>).

## Порты `TCP` и `UDP` для работы `Keycloak` для подключения к `FreeIPA`

Для подключения к `FreeIPA` нужно открыть порты:

```
TCP: 389 636 88 464
UDP: 88 464
```

## Устанавливаем `BigBlueButton` на сервер `Ubuntu`

Сначала нужно изменить записи ДНС в домене `company.ru`. Нужно внести запись о сервере `BigBlueButton` `bbb.company.ru`.

Сама установка `BigBlueButton`:

```
# apt install iputils-ping vim net-tools netcat bash-completion screen ca-certificates mc dos2unix language-pack-en nmap traceroute htop
# update-locale LANG=en_US.UTF-8
# systemctl set-environment LANG=en_US.UTF-8
# reboot
# wget https://raw.githubusercontent.com/bigbluebutton/bbb-install/v3.0.x-release/bbb-install.sh
# chmod +x bbb-install.sh
# ./bbb-install.sh -v jammy-300 -s bbb.company.ru -e admin@company.ru -g -k
```

### Ключи установки

-g - установим `Greenlight`. Web интерфейс для работы с `BigBlueButton`;

-k - установить `Keycloak`.

`Greenlight` для `BigBlueButton` - это простой веб-интерфейс для проведения вебинаров и веб-конференций. В нашей конфигурации он нужен.

## Что получится в результате запуска скрипта `bbb-install.sh`

В результате установятся сервисы и контейнеры необходимые для работы сервера `BigBlueButton`.

Некоторые из них: `GREENLIGHT`, `KEYCLOAK`, `NGINX`, `HAPROXY`, `POSTGRESSQL`, `LETSENCRYPT`.

Используем только этот скрипт для установки, обновления и после изменений конфигурации `BigBlueButton`.

Установится сертификат от `Letsencrypt`.

Пропишется в `CRON` служба обновления сертификата.

## Создание записи администратора для `BigBlueButton` (и почему это можно не делать)

Нужно сразу разделить учётные записи созданные в `Keycloak` и учётные записи созданные в `Greenlight`.

Вот так создаётся административная запись в `Greenlight`, [Creating an Admin Account](https://docs.bigbluebutton.org/greenlight/v3/install/#creating-an-admin-account):

```
# cd greenlight-v3
# docker exec greenlight-v3 bundle exec rake admin:create['admin','admin@company.ru','P@ssw0rd']
```

Почему это можно не делать, если потом будете использовать `Keycloak`?

При использовании службы `Keycloak` учётные записи, которые создали в `BigBlueButton`, будут _не видны_. Забавно то, что, если потом отключить `Keycloak` записи появятся.

Учётные записи созданные в `Greenlight` и `Keycloak` не смешиваются. При подключении `Keycloak` учётки `Greenlight` будут скрыты, но будут блокировать совпадающие учётки `Keycloak` Т.о. если есть учётная запись в `Greenlight`, и такая же учётная запись в `Keycloak`, то войти на сервер `BigBlueButton` не удастся.

[Замечание]
Если есть учётная запись в `Greenlight`, и такая же учётная запись в `Keycloak`, то войти на сервер `BigBlueButton` не удастся.

Придется отключать `Keycloak`, входить на сервер `BigBlueButton` с учетной записью `Greenlight` администратора и удалять совпадающую запись.

## Сервер `BigBlueButton` готов

Нужно перезагрузить сервер и зайти на web-страницу.

## Настраиваем `Keycloak`

В файле `/root/greenlight-run/docker-compose.yml` забираете пароль администратора `admin` `Keycloak` из значения `KEYCLOAK_ADMIN_PASSWORD`.

Идёте на страницу `https://bbb.company.ru/keycloak` для настройки подключения к `FreeIPA`.

Там выбираете в левой части `Administration Console`, вводите имя пользователя `admin` и пароль.

Слева, вверху видите раскрывающийся список. Первая запись `master`. Раскрываете список и щелкаете `Create Realm`. Создаёте новую область `greenlight`.

### Следующий шаг, создание клиента

Открываете `Keycloak`, выбираете `Administration Console`, выбираете слева область `greelight`, выбираете слева `Client`, `Create client`, заполняете `Client ID`, `Next`, включаете `Client authentication`, далее `Save`.

Выбирает слева `Client`, справа, в списках `Clients`, открываете только что созданного клиента `greenlight`, закладка `Settings`, поле `Valid redirect URIs`, вводите адрес сайта `BigBlueButton` `https://bbb.company.ru/*`

Открываете закладку `Credentials`, сохраняете для себя содержимое поля `Client secret`, позже вы используете это для заполнения `OPENID_CONNECT_CLIENT_SECRET`
в файле `.env`.

## Настраиваем подключение `Keycloak` к `FreeIPA`

Открываете `Keycloak`, выбираете `Administration Console`, выбираете слева область `greelight`, далее `User federation`, далее `Add new provider`, далее `LDAP`. Далее `Vendor` выбираете `Other`.

Дальше перечислю значения, которые необходимо заполнить:

| Опция настройки | Значение |
| ------------------ | ------------------------------------- |
| Connection URL | `ldap://freeipa.company.ru:389` |
| Connection timeout | 600 |
| Bind type | simple |
| Bind DN | uid=admin,cn=users,cn=accounts,dc=company,dc=ru |
| Bind credentials | P@ssw0rd |
| Edit mode | READ_ONLY |
| Users DN | cn=users,cn=accounts,dc=company,dc=ru |
| Username LDAP attribute | uid |
| RDN LDAP attribute | uid |
| UUID LDAP attribute | uidNumber |
| User object classes | inetOrgPerson, organizationalPerson |
| User LDAP filter | `(&(objectClass=inetOrgPerson)(!(nsaccountlock=TRUE))(mail=*))` |
| Search scope | One Level |
| Read timeout | 600 |
| Import users | ON |
| Sync Registrations | ON |
| Trust email | ON |

[Замечание]
Поле `User LDAP filter` настроено для отбора пользователей у которых _есть_ электронная почта.

[Замечание]
Отбор пользователей в созданном провайдере производится вручную. Но можно настроить и на периодическую синхронизацию.

### Добавляем отображение поля `uidNumber`

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `User federation`, далее `LDAP`, далее `Mappers`, далее `Add mapper`.

Дальше перечислю значения, которые необходимо заполнить:

| Опция настройки | Значение |
| ------------------ | ------------------------------------- |
| Name | uidNumber |
| Mapper type | user-attribute-ldap-mapper |
| User Model Attribute | uidNumber |
| LDAP Attribute | uidNumber |
| Read Only | ON |
| Force a Default Value | ON |

### Добавляем поиск групп

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `User federation`, далее `LDAP`, далее `Mappers`, далее `Add mapper`.

Дальше перечислю значения, которые необходимо заполнить:

| Опция настройки | Значение |
| ------------------ | ------------------------------------- |
| Name | ldap-groups |
| Mapper type | group-ldap-mapper |
| LDAP Groups DN | cn=groups,cn=accounts,dc=company,dc=ru |
| Group Name LDAP Attribute | cn |
| Group Object Classes | groupOfNames |
| Membership LDAP Attribute | member |
| Membership Attribute Type | DN |
| Membership User LDAP Attribute | uid |
| LDAP filter | `(&(objectClass=posixGroup)(\|(cn=group_name_*)))` |
| Mode | READ_ONLY |
| User Groups Retrieve Strategy | LOAD_GROUPS_BY_MEMBER_ATTRIBUTE |
| Member-Of LDAP Attribute | memberOf |
| Groups Path | / |

[Замечание]
Сначала я использовал значение для `User Groups Retrieve Strategy` `LOAD_GROUPS_BY_MEMBER_ATTRIBUTE`. Потом прочитал в [Руководство по управлению Keycloak](https://wiki.orionsoft.ru/zvirt/latest/keycloak/management-guide/), что правильнее использовать `GET_GROUPS_FROM_USER_MEMBEROF_ATTRIBUTE`.


И там же написано, что можно сопоставить атрибуты дополнительных полей групп. В поле `Mapped Group Attributes` добавить через запятую поля `whenCreated, description`.

Проверить атрибуты групп можно `Groups` далее `ldap_group`, далее `Group details`, далее `Attributes`.

### Проверим поля `firstName`, `lastName`

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `User Federation`, далее `LDAP`, далее `Mappers`.

#### Отображение `first Name`

| Опция настройки | Значение |
| ------------------ | ------------------------------------- |
| User Model Attribute | firstName |
| LDAP Attribute  | givenName  |

### Отображение `lastName`

| Опция настройки | Значение |
| ------------------ | ------------------------------------- |
| User Model Attribute | lastName |
| LDAP Attribute | sn |

## Синхронизация `Keycloak` и `FreeIPA`

Для отбора пользователей из `FreeIPA` нужно войти в провайдер `LDAP`, в правом верхнем угла нажать `Action` и выбрать `Sync all users`.

## Просмотр пользователей

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `Users`.

## Настройка сопоставления групп

Прочитал про сопоставления групп в [Прикручиваем LDAP-авторизацию...](https://habr.com/ru/articles/441112/) и [Общие понятия Keycloak](https://docs.truedev.ru/Auth/keycloak/keycloak/#roles).

Сделал сопоставление для групп.

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `Client scope`, далее `groups`, далее `Mappers`, далее `Add mapper`, далее `By configuration`, далее `Group membership`.

| Опция настройки | Значение |
| ------------------ | ------------------------------------- |
| Name | groups |
| Multivalued | ON |
| Token Claim Name | groups |
| Claim JSON Type | String |
| Add to ID token | ON |
| Add to access token | ON |
| Add to userinfo | ON |

Следующим шагом настройки сопоставления было добавление области действия `Client scope` `groups` клиенту `greenlight`.

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `Clients`, далее `greenlight`, далее `Client scope`, далее `Add client scope`, далее `groups`.

Но я засомневался, потому что информацию о включении групп в токен прочитал только в двух источниках, больше ни у кого этого нет. Поэтому я не стал этого делать. Проверил подключение к `BigBlueButton`: без этого тоже работает. Пользователи подключаются и создают комнаты. В общем, работает и так.

## Подключаем `Keycloak` к `Greenlight`

Открываете `Keycloak`, далее `Administration Console`, выбираете слева область `greelight`, далее `Realm settings`, далее закладка `General`.

Внизу щёлкаете по `OpenID Endpoint Configuration`.

Откроется дополнительное окно

В нём аккуратно выделяете строчку от `"issuer":` и до запятой, без кавычек.

Т.е. из строки

```
"issuer":"https://bbb.company.ru/keycloak/realms/greenlight"
```

получится

```
https://bbb.company.ru/keycloak/realms/greenlight
```

Затем в окне терминала открываете файл `/root/greenlight-v3/.env` и редактируете строки:

```
OPENID_CONNECT_CLIENT_ID=greenlight
OPENID_CONNECT_CLIENT_SECRET=**содержимое OPENID_CONNECT_CLIENT_SECRET**
OPENID_CONNECT_ISSUER=https://bbb.company.ru/keycloak/realms/greenlight
OPENID_CONNECT_REDIRECT=https://bbb.company.ru/
```

После этого делаем рестарт `Greelight`:

```
# docker compose down && docker compose up -d
# bbb-conf --restart
```

## Как сделать

### Как присвоить роль администратора для учетной записи в `Keycloak`

```
# docker exec -it greenlight-v3 bundle exec rake user:set_admin_role[user@company.ru]
```

Можно здесь посмотреть: [Creating an Admin Account](https://docs.bigbluebutton.org/greenlight/v3/install/#creating-an-admin-account)

## Проблемы

### При изменении в `User federation` и обновлении информации о пользователях появляется ошибка отображения

Нужно включить и выключить провайдера `LDAP` и обновить пользователей.

### При сопоставлении групп появляется ошибка

Нужно выключить `Preserve Group Inheritance` в настройке отображения `ldap-groups` для провайдера `LDAP` `User federation`.

## Основные команды, которые могут Вам потребоваться при работе через SSH-клиент

Чтение логов:

```
# docker logs -f greenlight-v3
```

Перезапуск `BigBlueButton`:

```
# bbb-conf --restart
```

Перезапуск `GreenLight`:

```
# cd ~/greenlight-v3
# docker-compose down
# docker-compose up -d
# bbb-conf --restart
```

Удаление всех записей вебинаров с сервера:

```
bbb-record --deleteall
```

Очистка кеша и логов на сервере:

```
bbb-conf --clean
```

### Файлы и директории, которые могут быть полезны

Конфигурационный файл `Greenlight`:

```
/root/greenlight/.env
```

Конфигурационный файл `Keycloack`:

```
/root/greenlight-v3/docker-compose.yml
```

## Дополнительные материалы

[Прикручиваем LDAP-авторизацию к Kubernetes](https://habr.com/ru/articles/441112/)

[Подключение LDAP пользователей и групп в Keycloak](https://docs.truedev.ru/Auth/keycloak/keycloak-map-ldap/#uidnumber-ldap-mapper)

[Руководство по управлению Keycloak](https://wiki.orionsoft.ru/zvirt/latest/keycloak/management-guide/)

[Настройка федерации c LDAP](https://wiki.orionsoft.ru/zvirt/latest/keycloak/management-guide/#_настройка_федерации_c_ldap)

[Install BigBlueButton](https://docs.bigbluebutton.org/administration/install/)

[External Authentication](https://bigbluebutton.github.io/greenlight_v3/gl3-external-authentication)

[Настройка Keycloak](https://support.kontur.ru/talk/52564-nastrojka_keycloak_dlya_integracii_s_tolkom)

[Интеграция Keycloak с FreeIPA](https://vk.com/wall-226121191_211)

[BigBlueButton в корпоративной сети](https://abakbot.com/ru/online-13/bigbluebutton)

[Установка BigBlueButton](https://blog.sedicomm.com/2020/05/01/poshagovoe-rukovodstvo-kak-ustanovit-i-nastroit-sistemu-videokonferentssvyazi-bigbluebottom-s-greenlight-na-ubuntu-linux/)

[def]: #статья-про-интеграцию-bigbluebutton-keyсloak-freeipa-черновик