# DevSecOps_Useful

Ansible-роли для базового hardening Linux-серверов (Debian/Ubuntu, RHEL/CentOS).  
Запускаются единым плейбуком `hardening.yml`.

## Роли

| Роль | Что делает |
|------|-----------|
| `hardening_packages` | Устанавливает auditd, fail2ban, lynis и др. |
| `ssh_hardening` | Настраивает `/etc/ssh/sshd_config`: отключает root login, парольную аутентификацию, ограничивает сессии |
| `password_policy` | Hardening `/etc/login.defs` + деплоит `pwquality.conf`, бэкап системных файлов |
| `network_hardening` | nftables ruleset (stateful firewall) + sysctl-параметры для сети и ядра |
| `fail2ban` | Защита от брутфорса SSH через fail2ban |
| `logrotate` | Ротация логов |
| `auto_upgrades` | Автоматические security-обновления (unattended-upgrades / dnf-automatic) |
| `auditd` | Правила аудита: изменения passwd/shadow/sudoers/sshd_config, setuid-вызовы, отказы доступа |

## Запуск

```bash
# Полный hardening
ansible-playbook hardening.yml -i inventory

# Выборочно по тегу
ansible-playbook hardening.yml -i inventory --tags ssh
ansible-playbook hardening.yml -i inventory --tags sysctl,auditd

# Пропустить роль
ansible-playbook hardening.yml -i inventory --skip-tags logrotate
```

## Теги

`packages` · `ssh` · `password_policy` · `nftables` · `sysctl` · `network_hardening` · `fail2ban` · `logrotate` · `auto_upgrades` · `auditd`

## Переменные

Переопределяются в `group_vars/all.yml`. Дефолты — в `<role>/defaults/main.yml`.

```yaml
ssh_port: 22
backup_dir: /var/backups/hardening
nftables_web_enabled: false
```
