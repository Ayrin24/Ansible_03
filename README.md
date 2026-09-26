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

ClickHouse — group_vars/clickhouse/vars.yml

````
clickhouse_version	22.3.3.44	Версия ClickHouse
clickhouse_deb_base_url	https://packages.clickhouse.com/deb/pool/main/c	Базовый URL для скачивания .deb
clickhouse_deb_arch	amd64	Архитектура для clickhouse-common-static
clickhouse_packages	clickhouse-client, clickhouse-server, clickhouse-common-static	Список пакетов
````

Vector — group_vars/vector/vars.yml

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

Результаты:

Запустите ansible-lint site.yml и исправьте ошибки, если они есть.

<img width="1007" height="48" alt="image" src="https://github.com/user-attachments/assets/ec36fd87-bef5-42ba-826d-7bfdbecd9bb4" />

Попробуйте запустить playbook на этом окружении с флагом --check.

<img width="1225" height="250" alt="image" src="https://github.com/user-attachments/assets/cb729a38-bb97-44c9-bce0-dc272b901cb7" />

Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.

<img width="1222" height="135" alt="image" src="https://github.com/user-attachments/assets/61dcff13-1f3c-4d19-9c6b-339d8b233569" />

<img width="968" height="366" alt="image" src="https://github.com/user-attachments/assets/3ee93607-5539-44b0-b33d-bd98c000d687" />

Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.

<img width="873" height="106" alt="image" src="https://github.com/user-attachments/assets/3b35f1dc-ef6c-4c37-90e1-cca1a01790f2" />

