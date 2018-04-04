# kubernetes GPU cluster node prepaire tasks 

## Description
kubernetes GPU node 사전 설치 playbook

## utils
* ansible(<2.3)
* ansible-vault
* docker 1.13
* cuda 9.1
* nvidia-docker2 2.0.2
* nvidia-container-runtime 1.1.1


## create inventory
```bash
[all]
k8s-master-01 ansible_ssh_host=yournodeip  ip=yournodeip
k8s-master-02 ansible_ssh_host=yournodeip ip=yournodeip
k8s-etcd-01 ansible_ssh_host=yournodeip ip=yournodeip
k8s-etcd-02 ansible_ssh_host=yournodeip  ip=yournodeip
k8s-etcd-03 ansible_ssh_host=yournodeip  ip=yournodeip
k8s-minion-01 ansible_ssh_host=yournodeip  ip=yournodeip
k8s-minion-02 ansible_ssh_host=yournodeip  ip=yournodeip
k8s-minion-03 ansible_ssh_host=yournodeip ip=yournodeip

[kube-master]
k8s-master-01
k8s-master-02

[etcd]
k8s-etcd-01
k8s-etcd-02
k8s-etcd-03

[kube-node]
k8s-minion-01
k8s-minion-02
k8s-minion-03

[k8s-cluster:children]
kube-node
kube-master

[all:vars]
ansible_ssh_user=ubuntu
ansible_ssh_become_user=ubuntu
ansible_ssh_private_key_file=/home/ubuntu/secret.pem
```

## usage
```bash
# become password encryption
## create become pass yaml
vi become-pass.yml
---
ansible_become_pass: password

## create vault pass
openssl rand -base64 2048 > vault.pass

ansible-vault encrypt become-pass.yml --vault-password-file=./vault.pass

# password파일 생성 후 내용 확인
ansible-vault view become-pass.yml --vault-password-file=./vault.pass

ansible-playbook -i inventory/inventory.cfg requirement_tasks.yml --vault-password-file=./vault.pass -b -vvv
```

## playbook
### requirement_task.yml
#### all nodes(include master)
* check the python installation

#### node
* install a docker daemon
* install a nvidia cuda
* install nvidia docker2
* change docker daemon.json
* restart docker

