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

ansible-playbook playbook.yml -i .host
```

### Access Application
http://hostname

