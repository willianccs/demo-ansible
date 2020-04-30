# demo-ansible

## Provisionando um cluster com Ansible

Projeto utilizado no webinar em parceria com a Digital Innovation One e o Opensanca, realizado no dia 28/04/2020.
Slides: https://speakerdeck.com/willianccs/provisionando-um-cluster-kubernetes-com-ansible
Gravação: https://youtu.be/URcC8iKDFKQ

## Pré-requisitos

* Instale o Ansible [guia para instalação](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)  


## Como usar

### Provisionamento

Para usar o arquivo vagrant:
---

1. Baixe e instale o [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Baixe e instale o [Vagrant](https://www.vagrantup.com/docs/installation/)
3. Abra um prompt de shell e `cd` na pasta que contém o arquivo Vagrant

Uma vez feito tudo isso, você pode simplesmente digitar `vagrant up ', e o Vagrant criará novas VMs e as configurará.

Quando a VM estiver em funcionamento (após a conclusão da instalação do vagrant e você voltar ao prompt de comando), você poderá fazer login nela via SSH, se desejar, digitando `vagrant ssh`.

Se preferir pode executar a playbook com a flag `provider=local_vm`.  
Irá realizar os passos acima _(testado somente em Linux Debian/Ubuntu)_:
```bash
ansible-playbook -i hosts provisioning.yml -e provider=local_vm --become --ask-become-pass
```

Para usar AWS:
---

1. Instalar AWS CLI [guia para instalação](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
2. Configurar o [profile](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html) no AWS CLI *
3. Instalar AWS SDK [(boto3)](https://pypi.org/project/boto3/)
```bash
pip install boto3
```
Executar a playbook com a flag `provider=aws_ec2`:
```bash
ansible-playbook -i hosts provisioning.yml -e provider=aws_ec2
```

### Variáveis
Você deve alterar para os valores desejados no arquivo `roles/base/vars/main.yml`.
```yaml
# provider: local_vms
virtualbox_version: 5.2
virtualbox_install_dkms: False  # default for 5.1 or higher as no longer required
vagrant_version: "2.2.7"
vagrant_tmp_dir: "/tmp/ansible_downloads/"
vagrant_tmp_deb: "{{vagrant_tmp_dir}}/vagrant_{{vagrant_version}}.deb"
vagrant_tmp_rpm: "{{vagrant_tmp_dir}}/vagrant_{{vagrant_version}}.rpm"
vagrant_url_deb: "https://releases.hashicorp.com/vagrant/{{vagrant_version}}/vagrant_{{vagrant_version}}_{{ansible_architecture}}.deb"
vagrant_url_rpm: "https://releases.hashicorp.com/vagrant/{{vagrant_version}}/vagrant_{{vagrant_version}}_{{ansible_architecture}}.rpm"

# provider: aws_ec2
instance_type: #instance ty
sec_group_name: opensanca
image: ami-07ebfd5b3428b6f4d
keypair: opensanca
region: us-east-1
count: 3
profile: opensanca
```

### Instalação do cluster Kubernetes

*  VM's local:
```bash
ansible-playbook -i hosts install-k8s-ec2.yml 
```

* AWS:

Adicionar a chave de acesso às máquinas com o `ssh-agent` e `ssh-add`:
```bash
ssh-agent
ssh-add [CHAVE].pem
```

Ajustar no arquivo `hosts` o valor da variável `kubernetes_role`. Deixando uma delas com o valor `master` e as demais com o valor `node` ;)  
Exemplo:
```ini
[k8s_public]
54.174.147.222 kubernetes_role=node
3.95.10.197 kubernetes_role=master
35.174.155.137 kubernetes_role=node

```
Após o ajuste executar a playbook:
```bash
ansible-playbook -i hosts install-k8s-ec2.yml
```

Para validar a instalação acesse a máquina __kubernetes_master__.
```bash
$ ssh user@kubernetes_master_public_ip
$ sudo su
# kubectl get nodes
# kubectl get pods --all-namespaces
```


## Contribuindo
PRs e feedbacks são sempre bem vindos.
>>>>>>> First commit
