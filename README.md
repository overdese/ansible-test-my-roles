# Repo for test my ansible roles

Роли для тестов подключаются сабмодулями.
Другие роли подключаются через гит сабтри.

## Подготовка к работе

```sh

# get git submodules
git submodule update --init --recursive

# enable virtualenv for python
python3 -m venv .venv
source .venv/bin/activate

# install prerequisites
python3 -m pip install -r requirements.txt
ansible-galaxy install -r requirements.yml
ansible-galaxy collection install -r requirements.yml

```

## раскатка плейбуков

Примеры раскаток плейбуков.

```sh
source .venv/bin/activate



# чек мод (проверка без изменений) для плейбуков для инвентори infra_test
ansible-playbook playbooks/me/vbox/infra_test/main.yml -i inventories/me/vbox/infra_test --check

# все плейбуки для инвентори infra_test
ansible-playbook playbooks/me/vbox/infra_test/main.yml -i inventories/me/vbox/infra_test

# раскатка плейбука с докером для инвентори infra_test
ansible-playbook playbooks/me/vbox/infra_test/on-perm/docker.yml -i inventories/me/vbox/infra_test

```
