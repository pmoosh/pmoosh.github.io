---
layout: post
title: "Write 'Hello World' with Ansible"
date: 2015-01-31 11:02:45 -0800
comments: true
categories: [Ansible, Hello World]
---
Write a 'Hello World' program with Ansible
------------------------------------------

Lately I work quite a bit with Ansible. There is a lot to like about it. Creating VM on AWS is particularly easy with Ansible. I was working on a playbook that had a dependency on the Ansible version itself so I toyed around with `omit`.

My playbook contained the following piece of code:

```yml
....
{%raw %}
type: "{{ 'pd-ssd' if ansible_version.full | version_compare('1.9', '>=') else omit }}"
...
```
So it basically should use the value `pd-ssd` if the ansible version is 1.9 or greater or omit the parameter completely as it not supported in earlier Ansible versions (The parameter is used for the gce_pd module)

I did test `omit` in a smaller test playbook and here is the extact:
```yml
- name: Hello World
  hosts: local
  gather_facts: no
  connection: local
  vars:
    disp: "{{ xxxx | default(omit) }}"

  tasks:
  - name: check
    debug:
      msg: "{{ disp }}"
```

So lets run this:

```
$  ansible-playbook -i hosts hello.yml

PLAY [Hello World] ************************************************************

TASK: [check exists] ******************************************
ok: [localhost] => {
  "msg": "Hello world!"
}

PLAY RECAP ********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0
```

Awesome
