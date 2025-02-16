# 1. Установка Certbot

- Для Ubuntu / Debian
```aiignore
sudo apt update && sudo apt install certbot
```

- Для CentOS / RHEL
```aiignore
sudo yum install epel-release -y
sudo yum install certbot -y
```

# 2. Получение сертификатов (Standalone)

Если у тебя ещё нет Nginx/Apache, можно запустить certbot в режиме standalone:
```aiignore
sudo certbot certonly --standalone -d alfa-pay.org -d www.alfa-pay.org
```
**На время генерации Nginx/Apache должны быть выключены, так как Certbot занимает 80/443 порты.**

Если хочешь автоматически остановить Nginx перед запуском:
```aiignore
sudo systemctl stop nginx
sudo certbot certonly --standalone -d alfa-pay.org -d www.alfa-pay.org
sudo systemctl start nginx
```

После этого сертификаты сохранятся в `/etc/letsencrypt/live/alfa-pay.org/`:

- Полный сертификат: `/etc/letsencrypt/live/alfa-pay.org/fullchain.pem`
- Закрытый ключ: `/etc/letsencrypt/live/alfa-pay.org/privkey.pem`

# 3. Получение сертификатов (Через Nginx)

Если Nginx уже запущен и работает, можешь использовать `certbot --nginx`:
```aiignore
sudo certbot --nginx -d alfa-pay.org -d www.alfa-pay.org
```
Certbot сам найдёт конфигурацию Nginx и автоматически добавит пути к SSL-сертификатам.

# 4. Автообновление сертификатов

Let's Encrypt выдаёт сертификаты на **90 дней**, поэтому нужно настроить автообновление:
Проверь, работает ли обновление вручную:
```aiignore
sudo certbot renew --dry-run
```

Если всё ок, добавь в `cron` автоматическое обновление:
```aiignore
sudo crontab -e
```
Добавь строку, чтобы обновлять сертификаты раз в день:
```aiignore
0 3 * * * certbot renew --quiet && systemctl reload nginx
```