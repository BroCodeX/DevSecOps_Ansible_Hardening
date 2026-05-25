# fail2ban

Деплоит jail-конфиги в `/etc/fail2ban/jail.d/`. Один шаблон — любое количество jail-ов.

## Переменные (`defaults/main.yml`)

| Переменная | Описание |
|------------|----------|
| `fail2ban_jails` | Список jail-ов. Каждый item → отдельный файл `<name>.local` |

Ключи внутри `options` соответствуют директивам fail2ban без изменений.

## Пример

```yaml
fail2ban_jails:
  - name: sshd
    options:
      enabled: true
      port: 22
      filter: sshd
      backend: systemd
      maxretry: 3
      findtime: 5m
      bantime: 1h

  - name: nginx-http-auth
    options:
      enabled: true
      port: http,https
      filter: nginx-http-auth
      logpath: /var/log/nginx/error.log
      maxretry: 5
      bantime: 10m
```
