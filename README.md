# Это вспомогательный репозиторий под [задачу](https://github.com/netology-code/mnt-homeworks/tree/MNT-7/08-ansible-02-playbook) из учебного курса
В задаче требовалось модифицировать ансибловский плейбук, создав для этого 
специальный репозиторий (вот этот), в частности:

> Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

Оговорка: плейбук рассчитан на установку в докер-контейнер, использующий образ centos:7. 
См. [docker-compose.yml](infrastructure/docker-compose.yml).

Из-за того, что используется готовый образ centos7, в котором нет sudo, установка 
включена в сценарий. Таким образом, он состоит из трех частей:
- оборудование контейнера (установка sudo)
- скачивание, установка и запуск компонентов Clickhouse
- скачивание, установка и запуск vector

Переменные для каждого хоста задаются в файле 
[vars.yml](playbook/group_vars/clickhouse/vars.yml). В данном случае у нас только 
один хост, поэтому один набор переменных.

Хосты (наш единственный в данном случае) определяется в [prod.yml](playbook/inventory/prod.yml)

Сам плейбук: [site.yml](playbook/site.yml)

Теги в сценарии не используются.

Что именно устанавливаем:
- Clickhouse 22.3.3.44 в составе следующих компонентов:
  - clickhouse-client
  - clickhouse-server
  - clickhouse-common-static
- Vector 0.22.2

Также репозиторий содержит файл `requirements.txt` на случай необходимости 
установить ansible в питоновское виртуальное окружение. Требования:
* Python >= 3.7

Последовательность действий:

```bash
# Создаем виртуальное окружение, на примере python3.9
python3.9 -m venv devnet08venv
# Обновляем pip
devnet08venv/bin/pip install --upgrade pip
# Устанавливаем ansible, ansible-lint
devnet08venv/bin/pip install -r requirements.txt
# Переходим в playbook
cd playbook
# Запускаем плейбук
sudo devnet08venv/bin/ansible-playbook -i inventory/prod.yml site.yml
```
Как вариант, можно сразу после установки окружения активировать его 
(`devnet08venv/bin/activate`) и вызывть всё, что в нем установлено обычными 
командами, без указания пути к интерпретатору.