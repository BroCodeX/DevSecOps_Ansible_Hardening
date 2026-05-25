# nftables

Деплоит ruleset из шаблона и скрипт ручного восстановления правил.

## Что деплоится

| Файл | Назначение |
|------|-----------|
| `{{ nftables_conf_path }}` | Ruleset, загружается сервисом nftables при старте |
| `{{ nftables_restore_script_path }}` | Bash-скрипт для ручного пересоздания правил через `nft` |

## Переменные (`defaults/main.yml`)

| Переменная | По умолчанию |
|------------|-------------|
| `nftables_conf_path` | `/etc/nftables.conf` |
| `nftables_restore_script_path` | `/usr/local/sbin/restore_nft_rules.sh` |

## Что в ruleset

- Loopback + established/related accept
- SYN flood / SYN scan protection (10/s)
- UDP scan protection (20/s)
- Stealth TCP scan drop (FIN/SYN, XMAS, NULL)
- Именованные счётчики для каждого типа трафика
