# Ansible Vault

## как использовать ansible-vault

1 ключ на весь проект:
* в `ansible.cfg` `vault_password_file = vault.key`

несколько ключей с vault-id:
* передавать нужный ключ/ключи при запуске плейбука
* в `ansible.cfg` добавить строчку `vault_identity_list=app1@.vault/app1.key, app2@.vault/app2.key`

## генерация ключа

```sh
openssl rand -base64 32 | tr -dc 'a-zA-Z0-9' | tee .vault/default.key
LVveycbnHhgFtiob3tlJzwB2gIEOdFi8gVII2SUeaDA
```

Ключ должен удовлетворять условиям https://docs.gitlab.com/ee/ci/variables/#masked-variables

## шифрование

```bash
ansible-vault encrypt_string 'password'
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          32353032353861663432386561363563613138343066343661303436313036393238386132323536
          6639353135346632626433316435323233306538346338300a336265633935303132353062636665
          61656236336362343061353735323765613561323735663863383633343237386332333133366131
          6463343239333364310a393033343636383866623134383138646430656261376131386336663934
          3631

# with vault-id
ansible-vault  encrypt_string 'password' --vault-id=app_name@.vault/default.key
!vault |
          $ANSIBLE_VAULT;1.2;AES256;app_name
          33383037616664396437666463633666663639303036393134363535356531373631343465383031
          3862383463383230363337336539353162323964636466620a333837366631366561373764636565
          61653965653530636433613639383937643833366531666266343133346266313864386264396664
          3839366166393163330a393136393531373666643430663965303530366334363663303336613963
          3462
Encryption successfu

```

В yaml манифесте `inventories/me/shared/file_name.yml`:

```yaml
---

my_password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;app_name
          33383037616664396437666463633666663639303036393134363535356531373631343465383031
          3862383463383230363337336539353162323964636466620a333837366631366561373764636565
          61653965653530636433613639383937643833366531666266343133346266313864386264396664
          3839366166393163330a393136393531373666643430663965303530366334363663303336613963
          3462
```

## расшифровка для дебага

 `my_password` из файла `inventories/me/shared/file_name.yml`

```sh
ansible  all -i 127.0.0.1, -m debug -a var=my_password -e "@inventories/me/shared/file_name.yml"
127.0.0.1 | SUCCESS => {
    "my_password": "password"
}

# custom file key
ansible  all -i 127.0.0.1, --vault-id=app_name@.vault/default.key -m debug -a var=my_password -e "@inventories/me/shared/file_name.yml"
127.0.0.1 | SUCCESS => {
    "my_password": "password"
}
```

## несколько ключей

```sh
# in playbook one key use
ansible-playbook -i environments/dev/inventory.yaml --vault-id=app_name@.vault/app_name.key main.yaml


# in playbook multiple key use
ansible-playbook -i environments/dev/inventory.yaml --vault-id=app_name@.vault/app_name.key --vault-id=app_noname@.vault/app_noname.key main.yaml
```

