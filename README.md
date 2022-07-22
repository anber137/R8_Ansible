# Домашнее задание к занятию "08.01 Введение в Ansible"

## Подготовка к выполнению

###1. Установите ansible версии 2.10 или выше.

### Ответ:

```
root@debian:/home/R81/hw81# ansible --version
ansible 2.10.8
  config file = None
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.9.2 (default, Feb 28 2021, 17:03:44) [GCC 10.2.1 20210110]

```

### 2. Создайте свой собственный публичный репозиторий на github с произвольным именем.

Ответ:

[ansible](https://github.com/anber137/R8_Ansible.git)

### 3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

### Ответ:

[playbook](https://github.com/anber137/R8_Ansible/tree/main/playbook)

## Основная часть

### 1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.

### Ответ:

```
some_fact: 12
```

```
root@debian:/home/R81/hw81/playbook# ansible-playbook site.yml -i inventory/test.yml

PLAY [Print os facts] ****************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************
[DEPRECATION WARNING]: Distribution debian 11.1 on host localhost should use /usr/bin/python3, but is using /usr/bin/python for 
backward compatibility with prior Ansible releases. A future Ansible release will default to using the discovered platform python for
 this host. See https://docs.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for more information. This 
feature will be removed in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [localhost]

TASK [Print OS] **********************************************************************************************************************
ok: [localhost] => {
    "msg": "Debian"
}

TASK [Print fact] ********************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP ***************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### 2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.

### Ответ:

[group_vars](./playbook/group_vars/all/examp.yml)

```
TASK [Print fact] ********************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
```

### 3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.

### Ответ:

```bash
docker run --name centos7 -d pycontribs/centos:7 sleep 600000000000
docker run --name ubuntu -d pycontribs/ubuntu:latest sleep 600000000000
```

```
root@debian:/home/R81/hw81/playbook/inventory# docker ps
CONTAINER ID   IMAGE                      COMMAND                CREATED              STATUS          PORTS     NAMES
718522a66de7   pycontribs/centos:7        "sleep 600000000000"   About a minute ago   Up 55 seconds             centos7
4be97880c663   pycontribs/ubuntu:latest   "sleep 600000000000"   3 hours ago          Up 3 hours                ubuntu
```

### 4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

### Ответ:

```
TASK [Print fact] ********************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}
```

### 5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.

### 6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.

### Ответ:

```
TASK [Print fact] ******************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}
```

### 7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.

### Ответ:

```bash
root@debian:/home/R81/hw81/playbook# ansible-vault encrypt group_vars/deb/examp.yml 
root@debian:/home/R81/hw81/playbook# ansible-vault encrypt group_vars/el/examp.yml 
```
### 8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.

### Ответ:

```
root@debian:/home/R81/hw81/playbook# ansible-playbook site.yml -i inventory/prod.yml 

PLAY [Print os facts] **************************************************************************************************************************************************
ERROR! Attempting to decrypt but no vault secrets found

root@debian:/home/R81/hw81/playbook# ansible-playbook site.yml -i inventory/prod.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] **************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host ubuntu should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior Ansible
 releases. A future Ansible release will default to using the discovered platform python for this host. See 
https://docs.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12. Deprecation 
warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ********************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### 9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.

### Ответ: 

Вывод списка модулей подключения:

```bash
ansible-doc -t connection -l 
```

Исходя из [концепции](https://docs.ansible.com/ansible/latest/network/getting_started/basic_concepts.html#control-node) `control node` -это компьютер с которого мы запускаем `ansible cli tools`, т.е. `local`, что подтверждается:

```bash
ansible-doc -t connection -s local
```

```
This connection plugin allows ansible to execute tasks on the Ansible 'controller' instead of on a remote host.

```

### 10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.

### Ответ:

```yaml
---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_connection: local
...
```

### 11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.

### Ответ:

```
root@debian:/home/R81/hw81/playbook# ansible-playbook site.yml -i inventory/prod.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] **************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************
[DEPRECATION WARNING]: Distribution debian 11.1 on host localhost should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior 
Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See 
https://docs.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12. Deprecation 
warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [localhost]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host ubuntu should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior Ansible
 releases. A future Ansible release will default to using the discovered platform python for this host. See 
https://docs.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12. Deprecation 
warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ********************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Debian"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Для `local` была использована [./all/examp.yml](./playbook/group_vars/all/examp.yml), что логично. Если верно - это друой `some_fact`, то необходимо для `local` создать другой.

### 12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

### Ответ: [README.md](./playbook/README.md)

