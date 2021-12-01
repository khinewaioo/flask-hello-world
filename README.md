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
- hosts: localhost
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
- hosts: localhost
  tasks:

    - name: clone repository
      git:
        repo: 'https://github.com/khinewaioo/flask-hello-world.git'
        dest: /home/ubuntu/flask_app
        version: circleci-editor/circleci-project-setup

    - name: install modules in a virtualenv
      pip:
        requirements: /home/ubuntu/flask_app/requirements.txt
        virtualenv: /home/ubuntu/env
        virtualenv_python: python3.8
```
