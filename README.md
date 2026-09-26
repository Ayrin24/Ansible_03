<<<<<<< HEAD
# Ansible Playbook: ClickHouse + Vector

## Описание

Playbook устанавливает и настраивает два сервиса на отдельных группах хостов:
=======
# Ansible Playbook: ClickHouse + Vector + LightHouse

## Описание

Playbook устанавливает и настраивает три сервиса на отдельных группах хостов:
>>>>>>> 3366dc21eb55b42d2c280a29cdcc2321e3418db9

- **ClickHouse** — колоночная СУБД для аналитики. Устанавливается из
  официальных `.deb`-пакетов, создаётся база данных `logs`.
- **Vector** — инструмент для сбора и трансформации логов. Устанавливается
  из официального `tar.gz`-архива, конфигурация деплоится через
  Jinja2-шаблон, настраивается systemd-юнит.
<<<<<<< HEAD
=======
- **LightHouse** — веб-интерфейс для работы с ClickHouse. Устанавливается
  Nginx, деплоится статика LightHouse, настраивается виртуальный хост.
>>>>>>> 3366dc21eb55b42d2c280a29cdcc2321e3418db9

Playbook **идемпотентен**: повторный запуск не вносит изменений, если
система уже находится в целевом состоянии.

## Требования

- Ansible 2.9+ (рекомендуется 2.15+)
- SSH-доступ к целевым хостам по ключу
- Python 3 на целевых хостах
<<<<<<< HEAD
- Хосты ClickHouse — Ubuntu 22.04 / 24.04
=======
- Хосты ClickHouse и LightHouse — Ubuntu 22.04 / 24.04
>>>>>>> 3366dc21eb55b42d2c280a29cdcc2321e3418db9
- Хосты Vector — любая Linux-система с systemd
- Пользователь с правами `sudo` (используется `become: true`)

## Структура проекта

```text
playbook/
├── site.yml                          # основной playbook
├── ansible.cfg                       # настройки Ansible
├── inventory/
│   └── prod.yml                      # инвентарь продуктивного окружения
├── group_vars/
│   ├── clickhouse/
│   │   └── vars.yml                  # переменные ClickHouse
<<<<<<< HEAD
│   └── vector/
│       └── vars.yml                  # переменные Vector
├── templates/
│   └── vector.yaml.j2                # Jinja2-шаблон конфига Vector
=======
│   ├── vector/
│   │   └── vars.yml                  # переменные Vector
│   └── lighthouse/
│       └── vars.yml                  # переменные LightHouse
├── templates/
│   ├── vector.yaml.j2                # Jinja2-шаблон конфига Vector
│   └── lighthouse.conf.j2            # Jinja2-шаблон конфига Nginx для LightHouse
>>>>>>> 3366dc21eb55b42d2c280a29cdcc2321e3418db9
├── screenshots/
│   ├── lint.png
│   ├── check.png
│   ├── diff-first.png
│   └── diff-second.png
└── README.md
<<<<<<< HEAD


````
Структура playbook
Play 1: Install Clickhouse
Скачивает clickhouse-common-static_<version>_amd64.deb отдельно
(у этого пакета суффикс amd64, а не all).

Скачивает clickhouse-client_<version>_all.deb и
clickhouse-server_<version>_all.deb в цикле.

Устанавливает clickhouse-common-static первым (от него зависят
остальные пакеты).

Устанавливает clickhouse-client и clickhouse-server.

Через meta: flush_handlers дожидается перезапуска сервиса.

Создаёт базу данных logs.

Play 2: Install and configure Vector
Создаёт системного пользователя и группу vector.

Скачивает vector-<version>-<arch>.tar.gz в /tmp/vector-download.

Распаковывает архив в /opt/vector (со --strip-components=2).

Создаёт симлинк /usr/local/bin/vector.

Деплоит конфиг из templates/vector.yaml.j2 в /etc/vector/vector.yaml.

Создаёт systemd-юнит /etc/systemd/system/vector.service.

Включает и запускает сервис.

Результаты:

Запустите ansible-lint site.yml и исправьте ошибки, если они есть.

<img width="1105" height="497" alt="image" src="https://github.com/user-attachments/assets/f95c359f-2406-447d-a993-af02e0d07038" />

<img width="1009" height="64" alt="image" src="https://github.com/user-attachments/assets/cb4ba838-9e8d-4a65-bd90-f9db6986039c" />

Попробуйте запустить playbook на этом окружении с флагом --check.

<img width="1221" height="456" alt="image" src="https://github.com/user-attachments/assets/3aa6ec39-8668-4a31-a7cd-3d93a14c4e3e" />

Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.

<img width="1872" height="1001" alt="image" src="https://github.com/user-attachments/assets/e223e737-ba7b-4fd8-8f28-f40b55ad320c" />

<img width="1397" height="1005" alt="image" src="https://github.com/user-attachments/assets/46116a5f-87df-48f8-88d9-44f30a85dc2d" />

<img width="1899" height="471" alt="image" src="https://github.com/user-attachments/assets/bbfe5509-6ee5-466e-80de-5b99afcfe566" />

<img width="1122" height="300" alt="image" src="https://github.com/user-attachments/assets/cf929f8c-cebe-4186-abf6-94c2420c335e" />

Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.

<img width="1896" height="749" alt="image" src="https://github.com/user-attachments/assets/073b8b77-a4fa-4be9-b6f9-10c40bb453bf" />






=======
````

Параметры (переменные):

ClickHouse — group_vars/clickhouse/vars.yml

Переменная	Значение по умолчанию	Описание
````
clickhouse_version	22.3.3.44	Версия ClickHouse

clickhouse_deb_base_url	https://packages.clickhouse.com/deb/pool/main/c	Базовый URL для скачивания .deb

clickhouse_deb_arch	amd64	Архитектура для clickhouse-common-static

clickhouse_packages	clickhouse-client, clickhouse-server, clickhouse-common-static	Список пакетов
````

Vector — group_vars/vector/vars.yml

Переменная	Значение по умолчанию	Описание
````
vector_version	0.42.0	Версия Vector

vector_arch	x86_64-unknown-linux-gnu	Архитектура сборки

vector_download_dir	/tmp/vector-download	Каталог для скачивания архива

vector_install_dir	/opt/vector	Каталог установки

vector_config_dir	/etc/vector	Каталог конфигурации

vector_data_dir	/var/lib/vector	Каталог данных

vector_user	vector	Системный пользователь

vector_group	vector	Системная группа
````

LightHouse — group_vars/lighthouse/vars.yml

Переменная	Значение по умолчанию	Описание
````
lighthouse_version	0.1.0	Версия LightHouse

lighthouse_download_url	https://github.com/VKCOM/lighthouse/archive/refs/heads/master.tar.gz	URL архива со статикой

lighthouse_install_dir	/var/www/lighthouse	Каталог для статики LightHouse

lighthouse_user	www-data	Владелец файлов

lighthouse_group	www-data	Группа файлов

nginx_listen_port	80	Порт, который слушает Nginx
````

Теги


Теги в playbook не используются. Для выборочного запуска
применяется флаг --limit:

````
# Только ClickHouse
ansible-playbook site.yml --limit clickhouse --diff

# Только Vector
ansible-playbook site.yml --limit vector --diff

# Только LightHouse
ansible-playbook site.yml --limit lighthouse --diff
````

Результат:

Запустите ansible-lint site.yml и исправьте ошибки, если они есть.

<img width="995" height="64" alt="image" src="https://github.com/user-attachments/assets/efc286cf-5f76-4531-8a48-ca6ef197dd3e" />

Попробуйте запустить playbook на этом окружении с флагом --check.

<img width="1905" height="253" alt="image" src="https://github.com/user-attachments/assets/0b0ca515-d814-47cf-bce2-ddbf28637d27" />

Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.

<img width="926" height="179" alt="image" src="https://github.com/user-attachments/assets/28f7383a-ade8-437f-b6b2-fda17af6d3fe" />

<img width="959" height="286" alt="image" src="https://github.com/user-attachments/assets/86284263-c159-4664-aabf-28b074d7f510" />

Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.

<img width="1227" height="207" alt="image" src="https://github.com/user-attachments/assets/5b24912b-c8d9-4ea5-b3c8-8fc41c66e2b3" />
>>>>>>> 3366dc21eb55b42d2c280a29cdcc2321e3418db9
