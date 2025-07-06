# Домашнее задание к занятию "`Работа с roles`" - `Татаринцев Алексей`

---

### Задание 1

1. `Создаю requirements.yml с наполнением`
```
---
- src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
  scm: git
  version: "1.13"
  name: clickhouse

```

2. `Устанавливаю роль ClickHouse`
```
ansible-galaxy install -r requirements.yml --force

```
![1](https://github.com/Foxbeerxxx/work_in_role/blob/main/img/img1.png)

3. `Теперь создаю файл site.yml (основной playbook)`
```
- hosts: all
  become: true
  roles:
    - clickhouse
    - vector
    - lighthouse

```

4. `Создаёю роль Vector`
```
ansible-galaxy init vector-role

```

5. `Захожу в нее , потом в  файл tasks/main.yml и добавляю туда:`

```
- name: Установить Vector
  get_url:
    url: "https://packages.timber.io/vector/latest/vector-x86_64.rpm"
    dest: "/tmp/vector.rpm"

- name: Установить пакет Vector
  yum:
    name: "/tmp/vector.rpm"

- name: Создать конфиг Vector
  template:
    src: vector.toml.j2
    dest: /etc/vector/vector.toml
    mode: "0644"

- name: Запустить Vector
  service:
    name: vector
    enabled: true
    state: started

```
6. `Также создам шаблон templates/vector.toml.j2 и вставлю туда`

```
[sources.my_source]
type = "stdin"

[sinks.my_sink]
type = "console"
inputs = ["my_source"]
encoding.codec = "text"
```

7. ` Создаю роль LightHouse`
```
ansible-galaxy init lighthouse-role

```

8. ` Открою tasks/main.yml и напишу наполнение`

```
- name: Установить git
  yum:
    name: git
    state: present

- name: Клонировать LightHouse
  git:
    repo: "https://github.com/VKCOM/lighthouse.git"
    dest: "/var/www/lighthouse"

```

9. ` Инициализирую git в каждой роли`

```
cd vector-role
git init
git remote add origin git@github.com:foxbeerxxx/vector-role.git
git add .
git commit -m "initial commit"
git push -u origin master
```
![2](https://github.com/Foxbeerxxx/work_in_role/blob/main/img/img2.png)
![3](https://github.com/Foxbeerxxx/work_in_role/blob/main/img/img3.png)


10. `То же самое проделаю для lighthouse-role и work_in_role`
11. ` Обновляю requirements.yml`

```
---
- src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
  scm: git
  version: "1.13"
  name: clickhouse

- src: git@github.com:foxbeerxxx/vector-role.git
  scm: git
  version: "1.0.0"
  name: vector

- src: git@github.com:foxbeerxxx/lighthouse-role.git
  scm: git
  version: "1.0.0"
  name: lighthouse

```

12. `И снова: ansible-galaxy install -r requirements.yml`
![4](https://github.com/Foxbeerxxx/work_in_role/blob/main/img/img4.png)

13. `все что у меня получилось`

```
Ссылки:
https://github.com/Foxbeerxxx/vector-role
https://github.com/Foxbeerxxx/lighthouse-role
https://github.com/Foxbeerxxx/work_in_role
```