Настройка SSH для подключения с домашнего компьютера к вашему VPS включает несколько основных шагов – от установки и генерации ключей до внесения изменений в конфигурацию SSH-сервера для повышения безопасности. Ниже приведена подробная пошаговая инструкция.

---

## 1. Подготовка на домашнем компьютере

### Выбор SSH-клиента
- **Linux/macOS:** SSH-клиент уже установлен. Откройте терминал.
- **Windows:** Можно использовать [PuTTY](https://www.putty.org/) или встроенный клиент Windows (например, в Windows Terminal). Например, если используете PuTTY, скачайте его с официального сайта.

### Генерация пары SSH-ключей (рекомендуется для повышения безопасности)
1. **Откройте терминал (или командную строку в Windows, если используете встроенный SSH-клиент).**
2. Выполните команду для создания пары ключей (пример для RSA с 4096 битами):
   ```
   ssh-keygen -t rsa -b 4096 -C "ваш_email@example.com"
   ```
   При этом вам будет предложено указать место сохранения ключа (по умолчанию — `~/.ssh/id_rsa`) и задать парольную фразу (рекомендуется).

---

## 2. Передача публичного ключа на VPS

После того как пара ключей сгенерирована, необходимо скопировать открытый ключ на ваш сервер. Это можно сделать с помощью утилиты **ssh-copy-id**:
```
ssh-copy-id username@IP_вашего_VPS
```
Где:
- **username** – имя пользователя, под которым вы планируете подключаться (например, не root, а обычный пользователь, созданный для администрирования),
- **IP_вашего_VPS** – IP-адрес вашего VPS.

Если утилита **ssh-copy-id** недоступна, можно вручную скопировать содержимое файла `~/.ssh/id_rsa.pub` и добавить его в файл `~/.ssh/authorized_keys` на сервере.

*Подробную процедуру можно найти, например, в статьях на [ATLEX.Ru](citeturn0search1) и [Рег.ру](citeturn0search0).*

---

## 3. Подключение к VPS через SSH

### Из домашнего терминала
- **Linux/macOS:** Выполните команду:
  ```
  ssh username@IP_вашего_VPS
  ```
  Если вы изменили стандартный SSH-порт (22), добавьте опцию `-p` с нужным номером, например:
  ```
  ssh username@IP_вашего_VPS -p 2222
  ```
- **Windows (с PuTTY):**
  1. Запустите PuTTY.
  2. В поле «Host Name (или IP-адрес)» введите IP вашего VPS.
  3. Убедитесь, что выбран тип соединения SSH и порт (по умолчанию 22, или другой, если вы его изменили).
  4. Нажмите кнопку «Open» и в появившемся окне введите имя пользователя и, при необходимости, пароль или используйте ключ для подключения.

---

## 4. Дополнительные настройки безопасности на VPS

Чтобы усилить защиту VPS, рекомендуется выполнить следующие шаги (эти действия выполняются уже на самом сервере):

### Измените настройки SSH-сервера
1. **Отключение входа под root:**
   Отредактируйте файл `/etc/ssh/sshd_config` (например, с помощью nano или vim):
   ```
   sudo nano /etc/ssh/sshd_config
   ```
   Найдите строку:
   ```
   PermitRootLogin yes
   ```
   и измените её на:
   ```
   PermitRootLogin no
   ```
2. **Отключение аутентификации по паролю:**
   Найдите строку:
   ```
   #PasswordAuthentication yes
   ```
   и измените её (или добавьте новую строку) на:
   ```
   PasswordAuthentication no
   ```
   Это заставит систему использовать только ключи SSH для входа.
3. **Изменение порта по умолчанию (опционально):**
   Чтобы снизить риск автоматизированных атак, можно изменить стандартный порт (22) на другой (например, 2222). Найдите строку:
   ```
   #Port 22
   ```
   и измените её на:
   ```
   Port 2222
   ```
4. **Перезапустите SSH-сервер, чтобы изменения вступили в силу:**
   ```
   sudo systemctl restart sshd
   ```

*Подробную настройку SSH можно изучить в руководствах на [ALexHost](citeturn0search9) и других источниках.*

---

## 5. Проверка подключения

После внесения изменений:
- Отключитесь от VPS (команда `exit`).
- Повторите подключение, используя SSH-клиент и указав новый порт (если изменяли) и ключ для аутентификации. Например:
  ```
  ssh username@IP_вашего_VPS -p 2222
  ```
- Если всё настроено правильно, вход произойдет без запроса пароля (либо будет запрошена парольная фраза ключа), а попытка подключения под root должна завершаться отказом.

---

## Заключение

Таким образом, настройка SSH для подключения с вашего домашнего компьютера к VPS включает:
1. Установку SSH-клиента (или использование встроенного клиента).
2. Генерацию пары ключей и передачу публичного ключа на сервер.
3. Подключение к серверу с использованием SSH.
4. Дополнительную настройку безопасности на стороне VPS (отключение входа root, аутентификации по паролю, изменение порта).

Следуя этим шагам, вы создадите безопасное и удобное подключение к вашему VPS для дальнейшего управления сервером.

*Дополнительные инструкции и советы можно найти в ряде русскоязычных материалов, например, на [Рег.ру](citeturn0search0) и [ALexHost](citeturn0search9).*
