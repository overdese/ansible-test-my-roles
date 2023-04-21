# Организация репозитория

## мои дополнения

Директории
- `inventories` рабочие, тестовые и всякое "актуальные" в данный момент инвентори. Думаю будут иметь временный характер.
- `inv_kwork` инвентори для задач по kwork
- `inv_overdese` мои личные инвентори



## дефолтовое

```
.
|--- .deploy    # докер файлы, gitlab ci шаблоны, kube манифесты и прочее нужное для деплоя
|--- .vault    # директория с vault файлами
    |--- default.key    # дефолтный vault файл (смотри `ansible.cfg` директиву `vault_password_file`)
|--- docs    # документация
|--- files    # директория для  с общими глобальными файлами, как ssh ключи, ssl серты
    |--- customer    # организация, компания: `me`, `roga_i_kopyta`, `kwork_username` (для заказчиков с kwork)
|--- inventories    # директория, содержащая инвентори файлы и прочие параметры окружений
    |--- customer    # организация, компания: `me`, `roga_i_kopyta`, `kwork_username` (для заказчиков с kwork)
        |--- provider    # провайдер, дц где расположены данные ресурсы: `on-prem`, `vbox`, `aws`, `gcp`, `vultr`
            |--- region    # регион в рамках провайдера, где располагаются ресурсы: `ru-central-1a`, `us-west-1`. Можно использовать `main`
                |--- project    # проект: `site-name`, `project-name`. Можно использовать `global`
                    |--- environment    # окружение: `dev`, `test`, `demo`, `prod`
                        |--- group_vars
                        |--- host_vars
                        |--- inventory.yaml
|--- playbooks    # плейбуки, один плейбук на приложение. Может содержать `main_app_name.yaml` для обеденения аппов в одну логическу структуру
    |--- customer
        |--- main.yml    # плейбук со всеми ролями для инфры кастомера
        |--- distribution    # тип установки/доставки приложения `docker`, `on-prem`
|--- roles    # роли
|--- templates    # шаблоны
    |--- customer
|--- vendor/roles    # импортированные роли
|--- ansible.cfg    # конфиг
|--- requirements.txt    # python requirements
|--- requirements.yml    # ansible playbooks' requirements
|--- main.yml    # плейбук со всеми плейбуками, выборка раскатки производится по тегу. т.е. один тег и плейбук полностью раскатывается с учетом зависимых плейбуков
```
