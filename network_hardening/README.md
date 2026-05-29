# nftables

Деплоит ruleset из шаблона и скрипт ручного восстановления правил.

## Что деплоится

| Файл | Назначение |
|------|-----------|
| `{{ nftables_conf_path }}` | Ruleset, загружается сервисом nftables при старте |
| `{{ nftables_restore_script_path }}` | Bash-скрипт для ручного пересоздания правил |

## Переменные (`defaults/main.yml`)

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `nftables_conf_path` | `/etc/nftables.conf` | Путь к ruleset |
| `nftables_restore_script_path` | `/usr/local/sbin/restore_nft_rules.sh` | Путь к restore-скрипту |
| `nftables_ssh_port` | `22` | SSH порт |
| `nftables_web_enabled` | `false` | Открыть tcp 80 и 443 |

## Что в ruleset

**Политика: `input drop`, `forward drop`, `output accept`**

| Правило | Действие |
|---------|----------|
| loopback | accept |
| ct state established,related | accept |
| ct state invalid | drop |
| tcp dport `ssh_port` ct state new | accept + counter |
| tcp dport 80,443 ct state new | accept (если `nftables_web_enabled`) |
| XMAS scan (все флаги) | drop + log + counter |
| NULL scan (нет флагов) | drop + log + counter |
| FIN/SYN scan | drop + log + counter |
| ICMP echo-request | accept |
| ICMPv6 echo + NDP | accept |

## Счётчики

```bash
nft list counters        # посмотреть все
nft reset counters       # сбросить
```

| Счётчик | Что считает |
|---------|------------|
| `c_ssh_accept` | Принятые SSH соединения |
| `c_stealth_drop` | Дропы XMAS и FIN/SYN сканов |
| `c_null_drop` | Дропы NULL сканов |
