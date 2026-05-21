# Установка Suricata + интеграция с Wazuh SIEM

Устанавливаю на dvwa-victim, логи подключаю к Wazuh агенту.

**Конфигурация VM:** dvwa-victim · 10.10.10.30

## Установка

```bash
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update
sudo apt install -y suricata
```

## Настройка

В `/etc/suricata/suricata.yaml` меняю:

**Домашняя сеть:**
```yaml
HOME_NET: "[10.10.10.0/24]"
```

## Обновление правил и запуск

```bash
sudo suricata-update
sudo systemctl enable suricata
sudo systemctl restart suricata
```

## Интеграция с Wazuh

В `/var/ossec/etc/ossec.conf` добавляю блок перед `</ossec_config>`:

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```

Wazuh агент читает файл `eve.json` и отправляет события на SIEM.

```bash
sudo systemctl restart wazuh-agent
```

## Проверка

```bash
sudo systemctl status suricata --no-pager
```

`eve.json` остаётся пустым пока нет трафика под правила Suricata.
Первые алерты появятся при nmap сканировании или брутфорсе с атакующей машины.