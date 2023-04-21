# Ansible Vault private key

```sh

# make new encrypt file
cp .vault/ssh_deploy_key/id_rsa .vault/ssh_deploy_key/id_rsa_encrypt
ansible-vault encrypt .vault/ssh_deploy_key/id_rsa_encrypt

```

## from stackoverflow

It's a bad idea to store any kind of plaintext secret in revision control, SSH private keys included. Instead, use ansible-vault to store the private key.

`ansible-vault` can operate on any file type. Just encrypt the file with

`ansible-vault encrypt /path/to/local/private_key`

then install the key:

```yaml
- name: Install a private SSH key
  vars:
    source_key: /path/to/local/private_key
    dest_key: /path/to/remote/private_key
  tasks:
  - name: Ensure .ssh directory exists.
    file:
      dest: "{{ dest_key | dirname }}"
      mode: 0700
      owner: user
      state: directory
  - name: Install ssh key
    copy:
      src: "{{ source_key }}"
      dest: "{{ dest_key }}"
      mode: 0600
      owner: user
```

Earlier versions of ansible-vault would only operate on variables defined in var files, so you had to do something like this:

```yaml
ssh_key: |
  -----BEGIN RSA PRIVATE KEY-----
  ...
  -----END RSA PRIVATE KEY-----
key_file: /home/user/.ssh/id_rsa
```

Encrypt with ansible-vault:

ansible-vault encrypt /path/to/var_file
And install the key:

```yaml
- name: Ensure .ssh directory exists.
  file:
    dest: "{{ key_file | dirname }}"
    mode: 0700
    owner: user
    state: directory

- name: Install ssh key
  copy:
    content: "{{ ssh_key }}"
    dest: "{{ key_file }}"
    mode: 0600
    owner: user
```

Thanks to all those below who improved the answer with their comments.
