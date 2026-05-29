# network_hardening

Деплоит nftables ruleset и применяет sysctl-параметры для hardening сети и ядра.

## Структура

```
tasks/
├── main.yml      — import_tasks для nft и sysctl
├── nft.yaml      — nftables ruleset + restore-скрипт
└── sysctl.yaml   — деплой /etc/sysctl.d/99-hardening.conf
```

---

## nftables

### Что деплоится

| Файл | Назначение |
|------|-----------|
| `{{ nftables_conf_path }}` | Ruleset, загружается сервисом nftables при старте |
| `{{ nftables_restore_script_path }}` | Bash-скрипт для ручного пересоздания правил |

### Переменные

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `nftables_conf_path` | `/etc/nftables.conf` | Путь к ruleset |
| `nftables_restore_script_path` | `/usr/local/sbin/restore_nft_rules.sh` | Путь к restore-скрипту |
| `nftables_ssh_port` | `22` | SSH порт |
| `nftables_web_enabled` | `false` | Открыть tcp 80 и 443 |

### Что в ruleset

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

### Счётчики

```bash
nft list counters   # посмотреть все
nft reset counters  # сбросить
```

| Счётчик | Что считает |
|---------|------------|
| `c_ssh_accept` | Принятые SSH соединения |
| `c_stealth_drop` | Дропы XMAS и FIN/SYN сканов |
| `c_null_drop` | Дропы NULL сканов |

---

## sysctl hardening

Деплоит `/etc/sysctl.d/99-hardening.conf` и применяет через `sysctl --system`.

### Переменные

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `sysctl_conf_path` | `/etc/sysctl.d/99-hardening.conf` | Путь к файлу параметров |
| `sysctl_params` | см. defaults | Список `{name, value}` |

### Параметры по умолчанию

| Параметр | Значение | Зачем |
|----------|----------|-------|
| `net.ipv4.conf.all.accept_redirects` | 0 | Запретить ICMP redirect (MITM) |
| `net.ipv4.conf.default.accept_redirects` | 0 | То же для новых интерфейсов |
| `net.ipv4.conf.all.send_redirects` | 0 | Не слать redirect (только роутеры) |
| `net.ipv4.conf.all.log_martians` | 1 | Логировать пакеты с невозможными адресами |
| `net.ipv4.conf.default.log_martians` | 1 | То же для новых интерфейсов |
| `net.ipv4.conf.all.rp_filter` | 1 | Strict reverse path filtering |
| `net.ipv6.conf.all.accept_redirects` | 0 | Запретить ICMPv6 redirect |
| `net.ipv6.conf.default.accept_redirects` | 0 | То же для новых интерфейсов |
| `kernel.yama.ptrace_scope` | 1 | Ограничить ptrace (только родитель→дочерний) |
| `kernel.kptr_restrict` | 2 | Скрыть адреса ядра из /proc |
| `kernel.sysrq` | 0 | Отключить Magic SysRq |

### Добавление параметра

```yaml
sysctl_params:
  - { name: 'net.ipv4.tcp_syncookies', value: 1 }
```
