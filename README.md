## 🛠️ Packer - Provadevops
Este repositório contém todos os arquivos necessários para o provisionamento das máquinas utilizadas no projeto Provadevops, incluindo o uso de ferramentas como Packer, Vagrant, Ansible e VirtualBox.

## 📋 Pré-requisitos
Antes de começar, verifique se os seguintes requisitos estão atendidos:

- [Packer](https://www.packer.io/downloads) instalado  
- [Vagrant](https://developer.hashicorp.com/vagrant/downloads) instalado  
- [Python](https://www.python.org/downloads/) instalado  
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) instalado  
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) instalado  
- Chave SSH gerada para acesso à máquina virtual

**⚠️ Os binários do Packer e Vagrant devem estar no mesmo diretório do arquivo packer.pkr.hcl.**


## **🚀 Passo a Passo**


**1. Clonar o repositório**
git clone https://github.com/seu-usuario/packer-provadevops.git


**2. Inicializar o Packer**
Execute os comandos abaixo para preparar o ambiente:
packer init .
packer plugin install github.com/hashicorp/virtualbox
packer plugin install github.com/hashicorp/vagrant

**3. Gerar a imagem com o Packer**
packer build debian.json
Isso criará a imagem .box baseada na configuração do arquivo debian.json.

**4. Adicionar a imagem ao Vagrant**
vagrant box add debian12 debian12.box

**5. Subir a máquina virtual com Vagrant**
vagrant up

## **🔐 Configurar acesso SSH**
**No terminal da máquina hospedeira, gere uma chave SSH (caso ainda não tenha):**
ssh-keygen
Pressione Enter em todas as opções. A chave será gerada em ~/.ssh/id_rsa.pub por padrão.

**Em seguida, copie a chave para a máquina virtual:**
ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@<IP_DA_VM>
Substitua <IP_DA_VM> pelo IP real da sua máquina virtual.

## **⚙️ Executar os playbooks Ansible**
Navegue até o diretório onde estão os arquivos Ansible e execute os seguintes comandos:
ansible-playbook -i hosts install_nginx.yml install_docker.yml install_kind.yml install_kubectl.yml
ansible-playbook -i hosts raise_nodes.yml
ansible-playbook -i hosts install_argocd.yml

## **♻️ Reiniciar o ArgoCD (quando necessário)**
Após o ambiente estar provisionado, se desejar hostear novamente o ArgoCD, execute:
ansible-playbook -i hosts start_argocd.yml

## 📁 Estrutura do Projeto
```bash
.
├── debian.json
├── packer.pkr.hcl
├── debian12.box
├── Vagrantfile
├── ansible/
│   ├── hosts
│   ├── install_nginx.yml
│   ├── install_docker.yml
│   ├── install_kind.yml
│   ├── install_kubectl.yml
│   ├── install_argocd.yml
│   ├── raise_nodes.yml
│   └── start_argocd.yml
```
