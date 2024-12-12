### Настройка автозагрузки Node.js приложений на CentOS с использованием systemd

Инструкция описывает, как настроить автозагрузку для Node.js приложений с использованием systemd. Эти шаги подходят для всех проектов, запускаемых через Node.js, включая использование `npx nodemon`.

---

#### **Шаг 1: Создание unit-файла**
1. Перейдите в каталог `/etc/systemd/system` и создайте unit-файл для вашего приложения:
   ```bash
   sudo nano /etc/systemd/system/<app_name>.service
   ```
   Замените `<app_name>` на имя вашего приложения (например, `my-node-app`).

2. Добавьте следующую конфигурацию в файл:
   ```ini
   [Unit]
   Description=Node.js App (<app_name>)
   After=network.target

   [Service]
   ExecStart=/usr/bin/npx nodemon /path/to/your/index.js
   Restart=always
   User=<username>
   WorkingDirectory=/path/to/your
   Environment=NODE_ENV=production
   StandardOutput=syslog
   StandardError=syslog
   SyslogIdentifier=<app_name>

   [Install]
   WantedBy=multi-user.target
   ```

   **Объяснение параметров:**
   - `ExecStart`: Команда для запуска приложения. Укажите полный путь к `npx` и вашему файлу `index.js`.
   - `User`: Имя пользователя, от которого будет запускаться приложение (например, `nodeuser` или текущий пользователь).
   - `WorkingDirectory`: Каталог, где находится приложение.
   - `Environment`: Установка переменных окружения (например, `NODE_ENV=production`).

---

#### **Шаг 2: Перезагрузка и включение службы**
1. Обновите systemd для применения изменений:
   ```bash
   sudo systemctl daemon-reload
   ```

2. Включите службу в автозагрузку:
   ```bash
   sudo systemctl enable <app_name>.service
   ```

3. Запустите службу:
   ```bash
   sudo systemctl start <app_name>.service
   ```

4. Проверьте статус службы:
   ```bash
   sudo systemctl status <app_name>.service
   ```

---

#### **Шаг 3: Управление службой**
- Перезапуск службы:
  ```bash
  sudo systemctl restart <app_name>.service
  ```

- Остановка службы:
  ```bash
  sudo systemctl stop <app_name>.service
  ```

- Отключение службы из автозагрузки:
  ```bash
  sudo systemctl disable <app_name>.service
  ```

---

#### **Примечания**
- Убедитесь, что Node.js и `npx` доступны из указанного пути. Проверьте это командой:
  ```bash
  which npx
  ```
  Если `npx` находится в другом месте, укажите полный путь в параметре `ExecStart`.

- Для production окружений рекомендуется использовать `node` вместо `nodemon`:
  ```ini
  ExecStart=/usr/bin/node /path/to/your/index.js
  ```
  Это сделает приложение более стабильным.

- Логи приложения можно просматривать через `journalctl`:
  ```bash
  sudo journalctl -u <app_name>.service
  ```

