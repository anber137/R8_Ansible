# Самоконтроль выполненения задания

1. Где расположен файл с `some_fact` из второго пункта задания?

`./playbook/group_vars/all/examp.yml`

2. Какая команда нужна для запуска вашего `playbook` на окружении `test.yml`?

```bash
ansible-playbook site.yml -i inventory/test.yml
```

3. Какой командой можно зашифровать файл?

```bash
ansible-vault encrypt filename
```

4. Какой командой можно расшифровать файл?

```bash
ansible-vault decrypt filename
```

5. Можно ли посмотреть содержимое зашифрованного файла без команды расшифровки файла? Если можно, то как?

```bash
ansible-vault view ./group_vars/deb/examp.yml
```

6. Как выглядит команда запуска `playbook`, если переменные зашифрованы?

Пример:

```bash
ansible-playbook site.yml -i inventory/prod.yml --ask-vault-pass
```

7. Как называется модуль подключения к host на windows?

```
ansible-doc -t connection -l | grep Micro
psrp                           Run tasks over Microsoft PowerShell Remoting...
winrm                          Run tasks over Microsoft's WinRM 
```

8. Приведите полный текст команды для поиска информации в документации ansible для модуля подключений ssh

```bash
ansible-doc -t connection -s ssh
```

9. Какой параметр из модуля подключения `ssh` необходим для того, чтобы определить пользователя, под которым необходимо совершать подключение?

```
ansible-doc -t connection -s ssh | grep user

- remote_user
```
