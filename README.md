# Ansible Playbook: ClickHouse + Vector + LightHouse

## Описание

Playbook устанавливает и настраивает три сервиса на отдельных группах хостов:

- **ClickHouse** — колоночная СУБД для аналитики. Устанавливается из
  официальных `.deb`-пакетов, создаётся база данных `logs`.
- **Vector** — инструмент для сбора и трансформации логов. Устанавливается
  из официального `tar.gz`-архива, конфигурация деплоится через
  Jinja2-шаблон, настраивается systemd-юнит.
- **LightHouse** — веб-интерфейс для работы с ClickHouse. Устанавливается
  Nginx, деплоится статика LightHouse, настраивается виртуальный хост.

Playbook **идемпотентен**: повторный запуск не вносит изменений, если
система уже находится в целевом состоянии.

## Требования

- Ansible 2.9+ (рекомендуется 2.15+)
- SSH-доступ к целевым хостам по ключу
- Python 3 на целевых хостах
- Хосты ClickHouse и LightHouse — Ubuntu 22.04 / 24.04
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
│   ├── vector/
│   │   └── vars.yml                  # переменные Vector
│   └── lighthouse/
│       └── vars.yml                  # переменные LightHouse
├── templates/
│   ├── vector.yaml.j2                # Jinja2-шаблон конфига Vector
│   └── lighthouse.conf.j2            # Jinja2-шаблон конфига Nginx для LightHouse
├── screenshots/
│   ├── lint.png
│   ├── check.png
│   ├── diff-first.png
│   └── diff-second.png
└── README.md
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
