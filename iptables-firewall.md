## Настройка файервола iptables в Linux

### 1. **Основные принципы iptables**
`iptables` — это встроенный межсетевой экран в Linux, который управляет сетевым трафиком. Он работает с цепочками правил и таблицами:

- **Таблицы:**
  - `filter` – фильтрация пакетов (основная).
  - `nat` – трансляция сетевых адресов.
  - `mangle` – модификация заголовков пакетов.
  - `raw` – обработка пакетов перед отслеживанием соединений.

- **Цепочки:**
  - `INPUT` – для входящих пакетов.
  - `FORWARD` – для пересылаемых пакетов.
  - `OUTPUT` – для исходящих пакетов.
  - `PREROUTING` – для изменения пакетов перед маршрутизацией.
  - `POSTROUTING` – для изменения пакетов перед отправкой.

---

### 2. **Просмотр текущих правил**
Перед настройкой рекомендуется посмотреть существующие правила:

```bash
sudo iptables -L -n -v
```
или для NAT-таблицы:
```bash
sudo iptables -t nat -L -n -v
```

---

### 3. **Очистка всех правил (при необходимости)**
Если требуется сбросить настройки:

```bash
sudo iptables -F
sudo iptables -X
sudo iptables -t nat -F
sudo iptables -t nat -X
sudo iptables -t mangle -F
sudo iptables -t mangle -X
```

Чтобы установить политики по умолчанию (закрыть доступ ко всему):

```bash
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT
```

---

### 4. **Разрешение основных сервисов**

#### Разрешаем loopback-интерфейс (localhost):
```bash
sudo iptables -A INPUT -i lo -j ACCEPT
```

#### Разрешаем уже установленные соединения:
```bash
sudo iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
```

#### Разрешаем SSH (22 порт):
```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

#### Разрешаем HTTP и HTTPS:
```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```

#### Разрешаем пинг:
```bash
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
```

---

### 5. **Блокировка подозрительных IP-адресов**
Пример блокировки конкретного IP:
```bash
sudo iptables -A INPUT -s 192.168.1.100 -j DROP
```

Блокировка диапазона:
```bash
sudo iptables -A INPUT -s 192.168.1.0/24 -j DROP
```

---

### 6. **Настройка NAT (маскарадинг)**
Если сервер должен раздавать интернет через NAT:
```bash
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
(где `eth0` – выходной интерфейс)

---

### 7. **Сохранение настроек**
В Fedora и RHEL:
```bash
sudo iptables-save > /etc/sysconfig/iptables
```

В Ubuntu/Debian:
```bash
sudo iptables-save > /etc/iptables.rules
```
Чтобы загрузить при старте:
```bash
sudo iptables-restore < /etc/iptables.rules
```

Для Fedora 40 можно использовать `nftables`, так как `iptables` заменяется `nft` в новых версиях.

---

### 8. **Просмотр и удаление правил**
Список правил с номерами:
```bash
sudo iptables -L --line-numbers
```
Удаление правила по номеру:
```bash
sudo iptables -D INPUT 3
```

Полностью очистить правила:
```bash
sudo iptables -F
```

---

### Заключение
Это базовая настройка `iptables`, обеспечивающая базовую защиту и доступ к необходимым сервисам. Для более сложных конфигураций можно использовать `iptables-persistent` или `firewalld` в современных дистрибутивах.
