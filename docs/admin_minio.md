# **Установка и настройка MinIO для проекта PPI**

Это руководство описывает процесс установки и настройки объектного хранилища MinIO на сервере Ubuntu 22.04 LTS для использования в проекте **Pedagogical Pattern Infrastructure (PPI)**.

## **Предварительные требования**

*   Сервер под управлением Ubuntu 22.04 LTS.
*   Права суперпользователя (`sudo`).
*   Открытые порты `9000` (для API) и `9001` (для веб-консоли) в вашем файрволе.

## **Шаг 1: Установка и базовый запуск MinIO**

### 1.1. Обновление системы
```bash
sudo apt update && sudo apt upgrade -y
```

### 1.2. Создание системного пользователя
Для безопасности MinIO будет работать от имени отдельного пользователя без прав на вход в систему.
```bash
sudo useradd -r -s /bin/false minio-user
```

### 1.3. Создание директорий
```bash
sudo mkdir -p /opt/minio/data
sudo chown minio-user:minio-user /opt/minio/data
```

### 1.4. Загрузка исполняемого файла MinIO
```bash
cd /tmp
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
sudo mv minio /usr/local/bin/
```

### 1.5. Создание файла конфигурации
Создайте файл, в котором будут храниться учетные данные администратора и другие настройки.
```bash
sudo nano /etc/minio/minio.env
```
Добавьте в него следующие строки. **Обязательно замените значения на свои собственные!**

```ini
# Замените на ваше имя пользователя администратора MinIO
MINIO_ROOT_USER=<your_admin_user>

# Замените на ваш НАДЕЖНЫЙ пароль администратора MinIO
MINIO_ROOT_PASSWORD=<your_strong_admin_password>

# Путь к директории с данными
MINIO_VOLUMES="/opt/minio/data"

# Указываем порт для веб-консоли
MINIO_OPTS="--console-address :9001"
```> **⚠️ Важно:** Используйте сложные, уникальные пароли для `MINIO_ROOT_PASSWORD` в рабочей среде.

### 1.6. Создание systemd-сервиса
Это позволит MinIO запускаться автоматически при старте системы.
```bash
sudo nano /etc/systemd/system/minio.service
```Вставьте в файл следующее содержимое:
```ini
[Unit]
Description=MinIO
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/usr/local/bin/minio

[Service]
WorkingDirectory=/usr/local/
User=minio-user
Group=minio-user
EnvironmentFile=/etc/minio/minio.env
ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES
Restart=always
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target```

### 1.7. Запуск сервиса MinIO
```bash
sudo systemctl daemon-reload
sudo systemctl enable minio
sudo systemctl start minio
sudo systemctl status minio
```
Если все в порядке, вы увидите статус `active (running)`.

### 1.8. Доступ к веб-консоли
Теперь вы можете зайти в веб-интерфейс MinIO по адресу `http://<your-server-ip>:9001`. Используйте логин и пароль, которые вы указали в файле `/etc/minio/minio.env`.

---

## **Шаг 2: Настройка MinIO для проекта PPI**

На этом шаге мы создадим отдельного пользователя для нашего веб-приложения, необходимые бакеты (buckets) и настроим права доступа.

### 2.1. Установка MinIO Client (mc)
`mc` — это утилита командной строки для управления MinIO.
```bash
cd /tmp
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/
```

### 2.2. Настройка подключения к вашему серверу
```bash
# Замените <your_admin_user> и <your_strong_admin_password> на ваши значения
mc alias set local http://localhost:9000 <your_admin_user> <your_strong_admin_password>
```
Проверьте подключение: `mc admin info local`.

### 2.3. Создание пользователя для приложения (Label Studio & Backend)
Мы создадим пользователя `labelstudio-user`, который будет использоваться нашим Flask-приложением для всех операций.
```bash
# Генерируем надежный секретный ключ
SECRET_KEY=$(openssl rand -hex 32)

# Создаем пользователя. Имя пользователя станет Access Key.
mc admin user add local labelstudio-user "$SECRET_KEY"

# Выводим сгенерированные ключи
echo "=========================================="
echo "Access Key: labelstudio-user"
echo "Secret Key: $SECRET_KEY"
echo "=========================================="
echo "⚠️ ВАЖНО: Сохраните эти ключи в безопасном месте!"
echo "Они понадобятся для конфигурации веб-приложения (config.py)."
```
> **Пример вывода (ваш Secret Key будет другим):**
> ```
> ==========================================
> Access Key: 
> Secret Key: 
> ==========================================
> ```

### 2.4. Создание бакетов для проекта PPI
```bash
mc mb local/raw-data
mc mb local/final-dataset
```
> **Примечание:** Мы создаем только два бакета, так как в текущей архитектуре проекта `teacher-patterns` и `student-patterns` являются скорее логическими категориями, а не физическими бакетами. Если они понадобятся, их можно создать аналогичным образом.

### 2.5. Создание и применение политик доступа
Политика определяет, какие действия пользователь `labelstudio-user` может совершать с бакетами.

```bash
# Создание файла политики
cat > /tmp/ppi-policy.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": [
        "arn:aws:s3:::raw-data",
        "arn:aws:s3:::final-dataset"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": [
        "arn:aws:s3:::raw-data/*",
        "arn:aws:s3:::final-dataset/*"
      ]
    }
  ]
}
EOF

# Создание политики в MinIO
mc admin policy create local ppi-policy /tmp/ppi-policy.json

# Применение политики к пользователю
mc admin policy attach local ppi-policy --user labelstudio-user
```
> **Объяснение:** Эта политика дает пользователю `labelstudio-user` полные права (чтение, запись, удаление, листинг) на бакеты `raw-data` и `final-dataset`, что необходимо для работы нашего приложения.

---

## **Шаг 3: Финальная настройка и проверка**

### 3.1. Настройка CORS
CORS (Cross-Origin Resource Sharing) необходим, чтобы Label Studio (работающий на порту `8080`) мог напрямую обращаться к MinIO (на порту `9000`) из браузера.
```bash
# Разрешаем запросы со всех источников. Для большей безопасности можно указать конкретный IP:порт.
mc admin config set local api cors_allow_origin="*"

# Перезапускаем сервер MinIO для применения настроек
mc admin service restart local
```

### 3.2. Настройка файрвола (UFW)
Если у вас включен файрвол, откройте порты для MinIO.
```bash
sudo ufw allow 9000/tcp
sudo ufw allow 9001/tcp
sudo ufw reload
```

**Поздравляем!** Ваше хранилище MinIO установлено и полностью настроено для работы с проектом PPI. Теперь вы можете использовать сгенерированные `Access Key` и `Secret Key` в файле `config.py` вашего веб-приложения.