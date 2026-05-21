# Настройка dvwa-victim

Установка DVWA, SSH и Wazuh агента на машину-жертву.

**Конфигурация VM:** dvwa-victim · 10.10.10.30 · 3 GB RAM · 30 GB диск

## Подготовка

Подключаюсь по SSH:

```bash
ssh umdv@10.10.10.30
```

Обновляю систему:

```bash
sudo apt update
sudo apt upgrade -y
```

## Установка Apache, PHP, MySQL

```bash
sudo apt install -y apache2 php php-mysqli php-gd libapache2-mod-php mysql-server git
sudo systemctl enable apache2 mysql
sudo systemctl start apache2 mysql
```

## Установка DVWA

```bash
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo cp /var/www/html/DVWA/config/config.inc.php.dist /var/www/html/DVWA/config/config.inc.php
sudo chown -R www-data:www-data /var/www/html/DVWA/
```

Создаю базу данных:

```bash
sudo mysql -u root <<'EOF'
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'dvwa_password';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
EOF
```

В конфиге `/var/www/html/DVWA/config/config.inc.php` устанавливаю:

```php
$_DVWA[ 'db_user' ]     = 'dvwa';
$_DVWA[ 'db_password' ] = 'dvwa_password';
```

Перезагрузка:

```bash
sudo systemctl restart apache2
```

Открываю `http://10.10.10.30/DVWA/setup.php` → **Create / Reset Database**.
Логин: `admin` / `password`. Уровень безопасности: **Low**.

## Настройка SSH

```bash
sudo apt install -y openssh-server
```

Создаю пользователя со слабым паролем — цель для дальнейшего брутфорса:

```bash
sudo useradd -m -s /bin/bash labuser
echo "labuser:password123" | sudo chpasswd
```

## Установка Wazuh агента

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor | sudo tee /usr/share/keyrings/wazuh.gpg > /dev/null
sudo chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt update
sudo apt install -y wazuh-agent
```

Прописываю IP SIEM в конфиге агента:

```bash
sudo sed -i 's/<address>MANAGER_IP<\/address>/<address>10.10.10.20<\/address>/' /var/ossec/etc/ossec.conf
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

## Снапшоты

- `clean` — сразу после установки OS, до агентов
- `clean (with agents)` — после установки Wazuh агента и Suricata

Перед каждым сценарием атаки буду откатывать ко второму.