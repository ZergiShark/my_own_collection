# Домашнее задание к занятию 6 «Создание собственных модулей»

## Решение

### Подготовка к выполнению

1. Создал пустой публичный репозиторий в своём проекте: `my_own_collection`.
2. Скачал репозиторий Ansible: `git clone https://github.com/ansible/ansible.git` в удобной мне директории.
3. Зашёл в директорию Ansible: `cd ansible`.
4. Создал виртуальное окружение: `python3 -m venv venv`.
5. Активировал виртуальное окружение: `. venv/bin/activate`. Последующие действия выполнялись только в виртуальном окружении.
6. Установил зависимости: `pip install -r requirements.txt`.
7. Запустил настройку окружения: `. hacking/env-setup`.
8. Если все шаги прошли успешно, то вышел из виртуального окружения: `deactivate`.
9. Окружение настроено. Чтобы запустить его, нужно находиться в директории `ansible` и выполнить команду: `. venv/bin/activate && . hacking/env-setup`.

### Основная часть

1. Создал файл [my_own_module.py](./my_own_module.py) 
2. Проверяю на исполняемость локально, используя файл [args.json](./args.json)
```
$ python3 -m ansible.modules.my_own_module args.json

{"changed": true, "invocation": {"module_args": {"path": "file.txt", "content": "test content"}}}
```
3. Написал [single_task_playbook.yml](./single_task_playbook.yml)
4. Проверил на идемпотентность:
```
$ ansible-playbook single_task_playbook.yml
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features under development. This is a rapidly changing source of code and can
become unstable at any point.
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test] ************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Create file] *****************************************************************************************************************************************************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
5. Вышел из локального окружения
6. Инициализировал новую collection: `ansible-galaxy collection init my_own_namespace.yandex_cloud_elk`
7. Перенёс модуль в collection `./netology/yamdex_cloud_elk/plugins/modules/my_own_module.py`
8. Single task playbook преобразовал в single task role и перенёс в collection. Поставил role default параметры module.
9. Сбилдил коллекцию
```
$ ansible-galaxy collection build
Created collection for my_own_namespace.yandex_cloud_elk at /home/azamat/module/ansible/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
```
10. [Коллекция](https://github.com/ZergiShark/collection/releases/tag/1.0.0) на GitHub
11. Установил коллекцию локально
```
$ ansible-galaxy collection install ./my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz 
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'my_own_namespace.yandex_cloud_elk:1.0.0' to '/home/azamat/.ansible/collections/ansible_collections/my_own_namespace/yandex_cloud_elk'
my_own_namespace.yandex_cloud_elk:1.0.0 was installed successfully
```
12. Написал [плейбук](./single_task_playbook.yml) под коллекцию
```
$ ansible-playbook single_task_playbook.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [test] ******************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************
ok: [localhost]

TASK [create file] ***********************************************************************************************************************************************************************************************
changed: [localhost]

PLAY RECAP *******************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```