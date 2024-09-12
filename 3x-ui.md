 # Пошаговая настройка сервера
  1. Меняем прароль пользователя и Root [passw "user login"] [passw]
  2. Обновляем систему [sudo apt update  && apt udgrade]
  3. Перезагрузка сервера [reboot]
  4. Устанавливаем панель 3x-ui (https://github.com/MHSanaei/3x-ui)
  bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)
				На вопрос о новой конфинурации или дефолной: Yes
				Вводим номер порта для WEB страницы:
				Вводим логин пользователя для WEB интерфейса:
				Вводим пароль для нового пользователя:
										Подтверждаем пароль:
				Вводим окончание WEB страницы (хаотичный ввод символов):
  5. Запускаем скрипт, который установит SSL-сертификат в панель для безопасного входа
	bash <(curl -Ls https://raw.githubusercontent.com/cyb3rm4gus/3x-ui-auto_add_ssl/main/3x-ui-autossl.sh)
  6. Установка Cloudflare WARP
		Вводим команду x-ui, в появившемся меню выбираем пункт 21, затем опцию 1. Порт устанавливаем 40000
  7.Установка fail2ban
		Вводим команду x-ui для вызова меню, выбираем пункт 20, затем опцию 1, дожидаемся завершения.
  8.Перезагрузка панели для применения настроек
		Вводим команду x-ui, в появившемся меню выбираем пункт 13
  9.Перезагрузка сервера [reboot]
  10.Заходим на сервер по адресу: https://ip_сервера:порт/web-base-path, порт и web-base-path вы указывали выше, и (надеюсь) 
		вы их сохранили. Если нет, вызовите меню командой x-ui и выберите пункт 10 для просмотра конфигурации.
  11. После ввода логина и пароля перейдите в раздел настройки xray, выберите WARP. Изменить настройки геопозиции.
		geosite:category-gov-ru,regexp:.*\.ru$,regexp:.*\.su$
  12. После этого обязательно нажмите вверху "сохранить" и "перезагрузить". Через меню x-ui
  13. Создание клиентов
		В настройках безопасности выбираем REALITY и жмем Get New Cert ниже.
		Раскрываем поле "клиент" и в Flow выбираем xtls-rprx-vision	
		Изменяем yahoo.com и www.yahoo.com на google.com и www.google.com
		В пункте uTLS выбираем chrome
		После этого жмем создать.
		Конфиг и сервер готовы к использованию.
  14. Поднимаем безопастность сервера
			Устанавливаем Firewall Management (ufw)
		Проверяем открытые порты системы формируем строку из портов через запятую без пробела пример(22,443,40000)
		Устанавливаем черех x-ui пункт 22(Firewall Management) пункт 1
		Вводим строку с портами для открытия
		Для добавления после установки UDP  портов команда ufw allow 14198/udp
		Для проверки ufw status
  15.	Защитим сервер от взлома через брутфорс:
			команда: touch /etc/fail2ban/jail.local && nano /etc/fail2ban/jail.local
			Вставляем 
[sshd]
enabled = true
filter = sshd
action = iptables[name=SSH, port=ssh, protocol=tcp]
logpath = /var/log/auth.log
findtime = 600
maxretry = 3
bantime = 43200
  16.	Отключаем двухсторонний пинг
			команда:  nano /etc/ufw/before.rules
			в 4 строчке раздела меняем с ACCEPT на DROP
  # ok icmp codes for INPUT
-A ufw-before-input -p icmp --icmp-type destination-unreachable -j ACCEPT
-A ufw-before-input -p icmp --icmp-type time-exceeded -j ACCEPT
-A ufw-before-input -p icmp --icmp-type parameter-problem -j ACCEPT
-A ufw-before-input -p icmp --icmp-type echo-request -j DROP