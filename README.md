# README

This is the [Flask](http://flask.pocoo.org/) [quick start](http://flask.pocoo.org/docs/1.0/quickstart/#a-minimal-application) example for [Render](https://render.com).

The app in this repo is deployed at [https://flask.onrender.com](https://flask.onrender.com).

## Deployment

Follow the guide at https://render.com/docs/deploy-flask.

## Deployment on Ubuntu
https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-16-04.


## Deploy by using Ansible
### Install Ansible
```
sudo apt install ansible
```

###  Run Playbook
```
ansible-playbook playbook.yml
```

### playbook.yml
```
# Ansible playbook for deploying a Flask app
---
# Install system apt packages
- hosts: webservers
  become: yes
  become_method: sudo
  tasks:
    - name: Update Cache
      apt:
        update_cache: true
        cache_valid_time: 3600
        force_apt_get: true

    - name: Install Dependencies
      apt:
       pkg:
        - python3-pip
        - python3-dev
        - nginx

# Install the app, note: don't do these tasks with become sudo
- hosts: webservers
  tasks:

    - name: clone repository
      git:
        repo: 'https://github.com/{{ github_user }}/{{ app_name }}.git'
        dest: /home/{{ ansible_ssh_user }}/{{ app_name }}
        version: circleci-editor/circleci-project-setup

    - name: install modules in a virtualenv
      pip:
        requirements: /home/{{ ansible_ssh_user }}/{{ app_name }}/requirements.txt
        virtualenv: /home/{{ ansible_ssh_user }}/{{ app_name }}/env
       
 - hosts: webservers
  become: yes
  become_method: sudo
  tasks:

  - name: template systemd service config
    template:
      src: .service
      dest: /etc/systemd/system/{{ app_name }}.service

  - name: start systemd app service
    systemd: name={{ app_name }}.service state=restarted enabled=yes

  - name: template nginx site config
    template:
      src: .nginx
      dest: /etc/nginx/sites-available/{{ app_name }}

  - name: remove default nginx site config
    file: path=/etc/nginx/sites-enabled/default state=absent

  - name: enable nginx site
    file:
      src: /etc/nginx/sites-available/{{ app_name }}
      dest: /etc/nginx/sites-enabled/default
      state: link
      force: yes

  - name: restart nginx
    systemd: name=nginx state=restarted enabled=yes

  - name: open firewall for nginx
    ufw:
      rule: allow
      name: Nginx Full


  # Run a quick test to verify the site is working
- hosts: webservers
  tasks:
  - name: get url
    get_url:
      url: http://{{inventory_hostname}}
      dest: /tmp/index.html
  - name: read html
    shell: cat /tmp/index.html
    register: html_contents
  - name: check for string in html
    when: html_contents.stdout.find('hello') != -1
    debug: msg="success!"
```
