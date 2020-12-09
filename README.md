# Setup

* 1 x VM target server par utilisateur + formateur  (Centos 8)
* 1 x VM client pour installation Ansible           (Centos 8)

* Setup AWS --> template terraform
* Voir pour un setup Katacoda.
* Voir setup suivi du lab dans un navigateur

## Objectif du lab

* Installer nextcloud (google drive on premice) avec Ansible en automatique

## Durée

* 1h
* 15mn pause
* 30 mn
* 15mn questions

# Lab

## Présentation Ansible

* Présentation du produit etc...
* Version act
* Gestion parc machines importante (normalement)
* Agent less
* Parallelisation par host
* Lent


* Arbo https://docs.ansible.com/ansible/2.3/playbooks_best_practices.html#content-organization
    * Inventaire et variables
    * Playbook
    * Role

* Module https://docs.ansible.com/ansible/2.8/modules/list_of_all_modules.html

* Astuces https://devdocs.io/ansible~2.9/modules/list_of_files_modules

* Galaxy
    * Bibliotheque en fonction des clients



##  Inventaire

1. Installation  (a faire par formateur)
    a. les manières
    b. les dependences
    c. pip
2. Statique / dynamique (cloud provider) --> statique
3. Construire inventaire (formateur + chaque utilisateur dans sa home)
    a. host
    b. group_vars --> all
    c. host_vars
4. Vérification setup
    a. Check cnx ssh
    b. Check connection avec Ansible  (ansible -m setup host)

## Playbook hello world

1. Playbook installation package sur target


    a. Playbook --> recette de cuisine
    b. gather_facts --> test cnx
    c. hosts --> inventaire
    d. tasks
    e. module --> voir doc module

```
---
- name: Installation apache
  gather_facts: yes
  hosts: front


  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
```


2. Modifier playbook pour utiliser root
    a. Editer sudoers
    b. Modifier playbook ajouter:
    ```
      become: yes
      become_user: root
    ```

3. Montrer idempotence
    a. relance playbook


4. Ajouter node db

```
---
- name: Installation apache
  gather_facts: yes
  hosts: front
  become: yes
  become_user: root


  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest

- name: update db servers
  hosts: back
  become: yes
  become_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum:
      name: postgresql
      state: latest
```

5. Ajouter le demarrage des services

```
---
- name: Installation apache
  gather_facts: yes
  hosts: front
  become: yes
  become_user: root


  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest

  - name: ensure that postgresql is started
    service:
      name: httpd
      state: started

- name: update db servers
  hosts: back
  become: yes
  become_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum:
      name: postgresql
      state: latest

  - name: ensure that postgresql is started
    service:
      name: postgresql
      state: started
```

6. Modularisation avec des roles


```
---
- name: Installation nexcloud
  gather_facts: yes
  hosts: front
  become: yes
  become_user: root


  roles:
    - role: front

- name: update db servers
  hosts: back
  become: yes
  become_user: root

  roles:
    - role: back
```

Role back:
```
  - name: ensure postgresql is at the latest version
    yum:
      name: postgresql
      state: latest

  - name: ensure that postgresql is started
    service:
      name: postgresql
      state: started

```

* Creation db + users
* Loop (création users)
* Templating conf
* Module vs shell
* cli nextcloud --> users  --> loop
* when
* debug
* register --> unique et list
* pattern debug (ignore errors + debug + fail)
* mode diff
* vault et secret
* cowsay (ultra bonus)
* vars --> en fn inventaire --> unicité de l'info.
* Récuperation de log (exploit)
* jinja (lb)
* set_fact
* variables prio et portée
* serial
* molecule  --> test (TU) + infra as code (donc bonne pratique de code) + on manipe plus à la main sur les machines




Role front

```
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest

  - name: ensure that postgresql is started
    service:
      name: httpd
      state: started

php + dep
installation depuis un tar de nextcloud

```



8. Ajouter un role de sanity checks (Tests)
