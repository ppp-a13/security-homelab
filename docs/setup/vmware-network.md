# Настройка сети в VMware Fusion

Создаю изолированную сеть для лаборатории: жертвы не имеют доступа в интернет, трафик между машинами идёт только внутри сети `vmnet2`.

## Создание сети

VMware Fusion Pro → Settings → Network → `+`

| Параметр | Значение |
|---|---|
| Сеть | vmnet2 |
| Тип | Host-only (Custom) |
| Subnet IP | 10.10.10.0 |
| Subnet Mask | 255.255.255.0 |
| DHCP | Включён |
| NAT | Выключен |

DHCP оставляю включённым, без него нельзя задать Subnet IP в интерфейсе.
Статические IP прописываю прямо внутри каждой VM, поэтому DHCP не мешает.

<img width="381" height="481" alt="Creating a network" src="https://github.com/user-attachments/assets/b1af5caa-e332-4660-bff2-40c4bf9f727a" />

## Назначение IP-адресов

| Машина | IP |
|---|---|
| kali | 10.10.10.10 |
| wazuh-siem | 10.10.10.20 |
| dvwa-victim | 10.10.10.30 |
| windows-victim | 10.10.10.40 |

## Статический IP на Ubuntu

При установке Ubuntu выбираю ручную настройку сети прямо в инсталляторе.

Для wazuh-siem:

| Параметр | Значение |
|---|---|
| Subnet | 10.10.10.0/24 |
| Address | 10.10.10.20 |
| Gateway | — |
| DNS | — |

<img width="1176" height="1036" alt="IP on Ubuntu Machine" src="https://github.com/user-attachments/assets/ba18b766-6a4e-44a6-b7c8-10b4e50fa5a3" />

## Проверка

После установки и входа по SSH проверяю интерфейсы:

```bash
ip a
```

<img width="1041" height="143" alt="Check IP" src="https://github.com/user-attachments/assets/39e6d64b-fb97-4abf-850d-394ae0731104" />

Должен быть `enp2s0` со статическим IP `10.10.10.20/24`.