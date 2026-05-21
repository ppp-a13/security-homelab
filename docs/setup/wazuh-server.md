# Установка Wazuh SIEM

Установка Wazuh 4.14.5 на Ubuntu 26.04 в режиме all-in-one:
Manager + Indexer + Dashboard на одной машине.

**Конфигурация VM:** wazuh-siem · 10.10.10.20 · 6 GB RAM · 60 GB диск

## Подготовка

Подключаюсь к VM по SSH:

```bash
ssh umws@10.10.10.20
```
<img width="551" height="83" alt="SSH connection" src="https://github.com/user-attachments/assets/76f647b9-9187-454c-8178-1c619915f0a8" />

Проверяю интернет:

```bash
ping -c 3 google.com
```

<img width="535" height="167" alt="Check SSH connection" src="https://github.com/user-attachments/assets/81608cbe-870a-4958-a059-58d89536a892" />

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

Все три сервиса должны быть в статусе: `active (running)`:

<img width="911" height="610" alt="Active status (1)" src="https://github.com/user-attachments/assets/ef8991f7-0d10-4ef6-a5d9-6397b2132d3f" />

<img width="914" height="420" alt="Active status (2)" src="https://github.com/user-attachments/assets/7182a4ec-f941-4142-b05c-a1922b393130" />

<img width="914" height="407" alt="Active status (3)" src="https://github.com/user-attachments/assets/116343ae-95d3-4fdb-8a5b-9e94914ab6c3" />

Открываю дашборд Wazuh в браузере:

```bash
https://10.10.10.20
```

<img width="1912" height="1161" alt="Wazuh dashboard" src="https://github.com/user-attachments/assets/9b0f1b18-2372-47df-b727-d9a7f7e4887a" />

Логин: `admin`, пароль из вывода установщика.

## Снапшот

После успешной установки делаю снапшот в VMware Fusion: `clean`.
Точка отката, если что-то сломается в процессе работы.

## Заметки

- Ubuntu 26.04 на данный момент не входит в официально поддерживаемые системы Wazuh, но установка и работа подтверждены на практике.
- NAT-адаптер на wazuh-siem нужен только для установки. После можно отключить.
- config.yml записывал через Python из-за проблем с heredoc в терминале Ghostty.