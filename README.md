## 🧾 Sobre o Projeto
Este repositório tem como objetivo fornecer uma infraestrutura automatizada para criação e provisionamento de máquinas virtuais utilizando ferramentas modernas de DevOps. Ele reúne todos os arquivos e instruções necessários para:

- Construção de uma imagem base com o Packer 

- Gerenciamento da máquina virtual com o Vagrant

- Automatização da configuração do ambiente com o Ansible

- Execução em ambiente local com o VirtualBox

A ideia é facilitar a criação de um ambiente de desenvolvimento ou testes que pode ser replicado de forma simples, padronizada e controlada.

Com este projeto, é possível provisionar uma VM Debian, instalar automaticamente ferramentas como Docker, Nginx, kubectl, Kind e ArgoCD, e levantar um cluster local com facilidade.

## 📋 Pré-requisitos
Antes de começar, verifique se os seguintes requisitos estão atendidos:

- [Packer](https://www.packer.io/downloads) instalado  
- [Vagrant](https://developer.hashicorp.com/vagrant/downloads) instalado    
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) instalado  
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) instalado  
- Chave SSH gerada para acesso à máquina virtual

**⚠️ Os binários do Packer e Vagrant devem estar no mesmo diretório do arquivo packer.pkr.hcl.**


## **🚀 Passo a Passo**


**1. Clonar o repositório**

```bash 
git clone https://github.com/seu-usuario/packer-provadevops.git
```


**2. Inicializar o Packer**

*Execute os comandos abaixo para preparar o ambiente:*

```bash 
packer init .
```

```bash 
packer plugin install github.com/hashicorp/virtualbox
```

```bash 
packer plugin install github.com/hashicorp/vagrant
```

**3. Gerar a imagem com o Packer**

```bash 
packer build debian.json
```

*Isso criará a imagem .box baseada na configuração do arquivo debian.json.*

**4. Adicionar a imagem ao Vagrant**

```bash 
vagrant box add debian12 debian12.box
```

**5. Subir a máquina virtual com Vagrant**

```bash 
vagrant up
```

## **🔐 Configurar acesso SSH**

*No terminal da máquina hospedeira, gere uma chave SSH (caso ainda não tenha):*

```bash
ssh-keygen
```

*Pressione Enter em todas as opções. A chave será gerada em ~/.ssh/id_rsa.pub por padrão.*

*Em seguida, copie a chave para a máquina virtual:*

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@<IP_DA_VM>
```

*Substitua <IP_DA_VM> pelo IP real da sua máquina virtual.*

**Lembre-se de sempre de modificar o arquivo Host**

## **⚙️ Executar os playbooks Ansible**

*Navegue até o diretório onde estão os arquivos Ansible e execute os seguintes comandos:*

```bash
ansible-playbook -i hosts install_nginx.yml install_docker.yml install_kind.yml install_kubectl.yml
```

```bash
ansible-playbook -i hosts raise_nodes.yml
```

```bash
ansible-playbook -i hosts install_argocd.yml
```

## **♻️ Reiniciar o ArgoCD (quando necessário)**

*Após o ambiente estar provisionado, se desejar hostear novamente o ArgoCD, execute:*

```bash 
ansible-playbook -i hosts start_argocd.yml
```
**Codigo para a key do argo**

```bash 
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo
```

## Acessar pelo navegador

**Suba a máquina virtual (se não tiver feito esse passo antes):**

*Faça sempre o comando abaixo se não tiver feito no dia que queria acessar a maquina*

```bash 
vagrant up
```

**Acesse a máquina virtual:**

```bash
ssh vagrant@<IP_DA_VM>
```

**Dentro da VM, execute o port-forward do serviço do frontend:**

```bash
kubectl port-forward svc/frontend-front-projetofevops --address 0.0.0.0 8000:80 --frontend
kubectl port-forward svc/frontend-front-projetofevops --address 0.0.0.0 8181:80 --backend
kubectl port-forward svc/frontend-front-projetofevops --address 0.0.0.0 8002:80 --banco mysql
```

**No seu navegador (fora da VM), acesse:**

```bash
http://<IP_DA_VM>:8000 --mude a porta
```

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


## 🔧 Tecnologias Utilizadas

**Packer**

Utilizado para criar uma imagem base personalizada, garantindo a padronização dos ambientes usados durante o desenvolvimento e testes.

**Vagrant**

Foi escolhido por ser uma ferramenta prática de gerenciamento de VMs, usando a imagem gerada pelo Packer. Ele facilita a automação e reprodutibilidade do ambiente de desenvolvimento.

**Ansible**

Responsável por provisionar e configurar automaticamente a máquina virtual, instalando os pacotes e preparando o ambiente necessário para o projeto.

**Kubectl e Kind**

Usados para gerenciar o cluster Kubernetes dentro da VM. O Kind permitiu criar clusters locais de forma leve e rápida, e o kubectl foi essencial para interagir com esses clusters e aplicar os recursos (pods, services, etc).

**Nginx**

Foi utilizado para expor serviços web dentro do cluster, sendo o intermediário entre o Kubernetes e o acesso externo. No caso do frontend, o Nginx atuou como servidor web.

**ArgoCD**

Ferramenta escolhida para realizar o gerenciamento de deployments no cluster Kubernetes, utilizando como base os arquivos armazenados nos repositórios do GitHub. Ele automatiza a entrega contínua de forma declarativa.

**Docker**

Foi utilizado para empacotar o frontend como container, garantindo que ele possa ser executado de forma isolada e consistente em qualquer ambiente (local ou cluster).
