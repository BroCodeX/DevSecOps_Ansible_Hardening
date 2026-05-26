# nftables

Деплоит ruleset из шаблона и скрипт ручного восстановления правил.

## Что деплоится

| Файл | Назначение |
|------|-----------|
| `{{ nftables_conf_path }}` | Ruleset, загружается сервисом nftables при старте |
| `{{ nftables_restore_script_path }}` | Bash-скрипт для ручного пересоздания правил через `nft` |

## Переменные (`defaults/main.yml`)

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `nftables_conf_path` | `/etc/nftables.conf` | Путь к ruleset |
| `nftables_restore_script_path` | `/usr/local/sbin/restore_nft_rules.sh` | Путь к restore-скрипту |
| `nftables_ssh_port` | `22` | SSH порт |
| `nftables_ssh_rate_limit` | `3` | Макс. новых соединений в минуту с одного IP |
| `nftables_ssh_rate_burst` | `3` | Burst поверх лимита |

## Что в ruleset

- Loopback + established/related accept
- SYN flood / SYN scan protection (10/s)
- UDP scan protection (20/s)
- Stealth TCP scan drop (FIN/SYN, XMAS, NULL)
- Именованные счётчики для каждого типа трафика
