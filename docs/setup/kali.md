# Настройка Kali Linux

**Конфигурация VM:** kali · 10.10.10.10 · 3 GB RAM · 40 GB диск

## Установка

Скачал ISO: `kali-linux-2026.1-installer-arm64.iso`  
Установка через Graphical install (Xfce + top10 + default tools).

## Сеть

Два сетевых адаптера:
- `eth0` → vmnet2 (лаб сеть, статический IP)
- `eth1` → Share with my Mac (NAT, интернет)

Статический IP-адрес задал через nmtui → Edit connection → IPv4 Manual: (address:10.10.10.10/24)

<img width="666" height="532" alt="IP setup (1)" src="https://github.com/user-attachments/assets/796d0f83-afdd-4f83-aded-37bc30255adc" />

<img width="666" height="532" alt="IP setup (2)" src="https://github.com/user-attachments/assets/8e144995-44b6-4f71-8d55-be6b0ce6a97e" />

Перезагрузил:

```bash
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

## Проверка связи

```bash
ping -c 3 10.10.10.30   # dvwa-victim
ping -c 3 10.10.10.20   # wazuh-siem
```

<img width="666" height="532" alt="Check connection" src="https://github.com/user-attachments/assets/483f29ed-b121-4c7c-be82-9bca90379d42" />

0% потеря пакетов, все работает.

## Снапшот

`clean` — сразу после установки и настройки сети.