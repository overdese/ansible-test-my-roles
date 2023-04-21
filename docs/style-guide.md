# Ansible Style Guide for Yaml

There are a few guidelines to follow when submitting code

## General

* Use lines less than 200 symbols
* Start your files with `---` on first line
* Always end your files with a newline
* YAML files - All yaml files should use 2 space indents and end with `.yaml`
* Jinja2 files - All jinja templates should end with `.j2`
* Use spaces around jinja variable names. `{{ var }}` not `{{var}}`
* Variables that are internal to the role should be lowercase.
* Prefix all variables defined in a role with the name of the role. Example: `rolename_foo`
* Keep roles self contained - Roles should avoid including tasks from other roles when possible
* Plays should do nothing more than include a list of roles except where pre_tasks and post_tasks are required (to manage a load balancer for example)
* Handlers - Any service requiring to restarted should be done via handlers. In other words, no hardcoded reboots of any service to include the host itself.

## Conditionals and return status

* Always use `when:` for conditionals - To check if a variable is defined `when: my_var is defined` or `when: my_var is not defined`
* To learn more  (see [conditional execution](http://docs.ansible.com/playbooks_conditionals.html#the-when-statement))

## Formatting

### Long lines

Break long lines using yaml line continuation. Reference: http://docs.ansible.com/playbooks_intro.html

good:
```yaml
- shell: >
    python a very long command --with=very --long-options=foo
    --and-even=more_options --like-these
```

### Key value pairs

Use only one space after the colon when designating a key value pair

bad:
```yaml
- name : 'start sensu-client'
  service:
    name    : 'sensu-client'
    state   : 'restarted'
    enabled : true
  become : true
```

good:
```yaml
- name: start sensu-client
  service:
    name: sensu-client
    state: restarted
    enabled: true
  become: true
```

### Map syntax

Always use the map syntax, regardless of how many pairs exist in the map

bad:
```yaml
- name: create checks directory to make it easier to look at checks vs handlers
  file: 'path=/etc/sensu/conf.d/checks state=directory mode=0755 owner=sensu group=sensu'
  become: true

- name: copy check-memory.json to /etc/sensu/conf.d
  copy: 'dest=/etc/sensu/conf.d/checks/ src=checks/check-memory.json'
  become: true
```

good:
```yaml
- name: create checks directory to make it easier to look at checks vs handlers
  file:
    group: sensu
    mode: 0755
    owner: sensu
    path: /etc/sensu/conf.d/checks
    state: directory
  become: true

- name: copy check-memory.json to /etc/sensu/conf.d
  copy:
    dest: /etc/sensu/conf.d/checks/
    src: checks/check-memory.json
  become: true
```

### Include Declaration

For `include` statements, make sure to quote filenames and only use blank lines between `include` statements if they are multi-line (e.g. they have tags).

bad:
```yaml
- include: other_file.yaml

- include: 'second_file.yaml'

- include: third_file.yaml tags=third
```

good:
```yaml
- include: 'other_file.yaml'
- include: 'second_file.yaml'

- include: 'third_file.yaml'
  tags: 'third'
```

### Variable Names

Use [snake_case](https://en.wikipedia.org/wiki/Snake_case) for variable names in your scripts.

bad:
```yaml
- name: set some facts
  set_fact:
    myBoolean: true
    myint: 20
    MY_STRING: 'test'
```

good:
```yaml
- name: set some facts
  set_fact:
    my_boolean: true
    my_int: 20
    my_string: 'test'
```

### Indent child elements

* 2 spaces for indentation
* always indent child elements
* do not align values

bad:
```yaml
modules:
- name: foo
  sources:
  - type: bar

id:           org.example.Foo
modules:
  - name:     foo
    sources:
      - type: git
```

good:
```yaml
modules:
  - name: foo
    sources:
      - type: bar
```

## Roles

### Role Variables

* Roles variables - Variables specific to a role should be defined in `some_role/defaults/main.yaml`. All variables should be prefixed with the role name.

### Role naming conventions

* Role names - Terse, lowercase, one word if possible. If necessary `-` sure to use.
* Role task names - Terse, descriptive, spaces are OK
* Role handlers - Terse, descriptive, spaces are OK

## Secure vs. Insecure data

As a general policy we want to protect the following data:

* Public keys (keys are OK to be public)
* Passwords, API keys, Tokens (must be encrypted)
* Secure output (must be with `no_log: true`)

# License

MIT
