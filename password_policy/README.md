# password_policy

Настраивает политику паролей в `/etc/login.defs`, выставляет права на системные файлы, делает бэкап.

## Переменные (`defaults/main.yml`)

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `backup_dir` | `/var/backups/hardening` | Директория бэкапов |
| `login_defs_path` | `/etc/login.defs` | Путь к файлу политик |
| `system_files` | passwd, shadow, login.defs | Список файлов для бэкапа и chmod/chown |
| `password_policy` | см. defaults | Список `{regexp, line}` для lineinfile |

`shadow_group` (`shadow` на Debian, `root` на RedHat) приходит из `vars/`.

## Добавление файла в бэкап / права

```yaml
system_files:
  - path: /etc/sudoers
    owner: root
    group: root
    mode: '0440'
```

## Изменение политики

```yaml
password_policy:
  - { regexp: '^PASS_MAX_DAYS', line: 'PASS_MAX_DAYS 60' }
  - { regexp: '^PASS_MIN_LEN',  line: 'PASS_MIN_LEN 16' }
  - { regexp: '^PASS_WARN_AGE', line: 'PASS_WARN_AGE 14' }
```
