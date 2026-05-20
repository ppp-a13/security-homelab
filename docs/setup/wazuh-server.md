# Установка Wazuh SIEM

Установка Wazuh 4.14.5 на Ubuntu 26.04 в режиме all-in-one:
Manager + Indexer + Dashboard на одной машине.

**Конфигурация VM:** wazuh-siem · 10.10.10.20 · 6 GB RAM · 60 GB диск

## Подготовка

Подключаюсь к VM по SSH:

```bash
ssh umws@10.10.10.20
```

Проверяю интернет:

```bash
ping -c 3 google.com
```

## Установка

Скачиваю установочный скрипт и создаю конфигурацию:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
```

```bash
python3 -c "
content = '''nodes:
  indexer:
    - name: node-1
      ip: \"10.10.10.20\"
  server:
    - name: wazuh-1
      ip: \"10.10.10.20\"
  dashboard:
    - name: dashboard
      ip: \"10.10.10.20\"
'''
open('config.yml', 'w').write(content)
"
```

Генерирую сертификаты:

```bash
sudo bash wazuh-install.sh --generate-config-files
```

Запускаю полную установку (занимает ~10 минут):

```bash
sudo bash wazuh-install.sh -a
```

В конце установщик выводит пароль, обязательно сохраняю.

## Проверка

```bash
sudo systemctl status wazuh-manager wazuh-indexer wazuh-dashboard --no-pager
```

Все три сервиса должны быть в статусе: `active (running)`.

Открываю дашборд Wazuh в браузере:

```bash
https://10.10.10.20
```

Логин: `admin`, пароль из вывода установщика.

## Снапшот

После успешной установки делаю снапшот в VMware Fusion: `wazuh-clean`.
Точка отката, если что-то сломается в процессе работы.


## Заметки

- Ubuntu 26.04 на данный момент не входит в официально поддерживаемые системы Wazuh, но установка и работа подтверждены на практике.
- NAT-адаптер на wazuh-siem нужен только для установки. После можно отключить.
- config.yml записывал через Python из-за проблем с heredoc в терминале Ghostty.