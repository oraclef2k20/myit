# ansible vault

https://docs.ansible.com/ansible/latest/user_guide/vault.html#ansible-vault

https://docs.ansible.com/ansible/latest/cli/ansible-vault.html#ansible-vault


example
--
`s.yml`
```yml
---
- hosts: amz2
  gather_facts: no
  vars_files:
    - p.yml
  tasks:
    - debug: msg="password = {{ password }}"
```
`p.yml`
```yml
---
password: 'hogehoge'
```

```console
$ ansible-playbook -i inventory s.yml

PLAY [amz2] **********************************************************************************

TASK [debug] *********************************************************************************
ok: [amz2] => {
    "msg": "password = hogehoge"
}

PLAY RECAP ***********************************************************************************
amz2         : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### encrypt p.yml(already existed)

```console
$ ansible-vault encrypt p.yml
New Vault password:
Confirm New Vault password:
Encryption successful
```
```console
$ cat p.yml
$ANSIBLE_VAULT;1.1;AES256
30316439396130333736326238626330316633653536316138616138313035343861633861353337
6136636631643935336130313238316365636331326465620a306538653335653033383232393861
33336237663534633462333563643635313162313063623036393162613730333434393930646361
3365653533393032370a666638653634373435346437326462363863393231356134316632373532
62643634343065303664353539666436323666653362623166386534656639353835
```

### decrypt when exec ansible-playbook

```console
$ ansible-playbook -i inventory s.yml --ask-vault-pass
Vault password:

PLAY [amz2] **********************************************************************************

TASK [debug] *********************************************************************************
ok: [amz2] => {
    "msg": "password = hogehoge"
}

PLAY RECAP ***********************************************************************************
amz2   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


### encrypt p2.yml(new file)

```console
$ ansible-vault create p2.yml --vault-id p2@prompt
New vault password (p2):
Confirm new vault password (p2):
```
After providing a password, the tool will launch whatever editor

`/tmp/tmpQQeCP.yml`
```yml
---
password2: 'foobarbaz'
```

```console
$ cat p2.yml                             
$ANSIBLE_VAULT;1.2;AES256;p2
35393335363335663736393437373362313031653534386166663035376163303839613535386165
6636626163353537343065626164633864613765306263650a376230376364633630656138323630
31383738623837306465666661616532633861343132663836613332343731363136336133626232
3032373861353639340a343663336264633833306563353537373036386466616234383262666431
63623262626632353061376639333966366232616131326134306336343261346130
```
