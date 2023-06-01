# Primeiros passos com Ansible

## whaomi

**Emerson Silva**

Atuo como DevOps Engineer,  possuo experiência em Administração de Sistemas Open Source, Virtualização, Cloud e ferramentas voltadas a práticas DevOps, com ênfase em IaC e CI/CD. 
Atuando há 6 anos na área de Tecnologia da Informação.
Detenho expertise como instrutor de cursos voltados para Containers, Infraestrutura Ágile CI/CD.

- **Certificações**
  - GitLab Associate
  - Scrum Essentials
  - DevOps Essentials
  - AWS Certified Cloud Practitioner

Mais sobre mim: [https://linktr.ee/silvemerson](https://linktr.ee/silvemerson)

# Roteiro do Talk

 - [Falando brevemente sobre DevOps](#falando-brevemente-sobre-devops)
 - [Automação de infraestrutura](#automação-de-infraestrutura)
 - [Orquestração de recursos](#orquestração-de-recursos)
 - [O que é o Ansible](#o-que-é-o-ansible)
 - [Inicializando o ambiente](#inicializando-o-ambiente)
 - [Instalando o Ansible](#inicializando-o-ambiente)
 - [Entendendo a estrutura inicial](#entendendo-a-estrutura-inicial)
 - [Ansible Ad hoc](#ansible-ad-hoc)
 - [Criando nossa Playbook](#criando-nossa-playbook)


## Falando brevemente sobre DevOps

DevOps que é a junção de Development e Operations, trata-se de uma forma de trabalhar, uma forma de pensar. Uma cultura que enfatiza a comunicação entre os profissionais de desenvolvimento e operações, tratando as equipes com um unico time para que juntos possam realizar entregas com exelência. 

As ferramentas sozinhas não são DevOps, o que as tornam DevOps é a forma que em como utilizamos. 

Objetivos de um ambiente com práticas DevOps:

• Melhorar a frequência dos deploys;

• Automatizar processos;

• Diminuir a ocorrência de erros em novas versões;

• Curtos períodos de tempo para mudanças e melhorias;

• Recuperação rápida em caso de falhas no ambiente;

• Padronização nos processos de configuração e servidores;



## Automação de infraestrutura

Automatizar a infraestrutura de TI refere-se à implementação de processos e ferramentas que permitem o gerenciamento e provisionamento automáticos dos recursos de infraestrutura, como servidores, redes, armazenamento e sistemas operacionais. A automação da infraestrutura tem como objetivo agilizar as operações de TI, reduzir erros humanos, aumentar a eficiência e melhorar a escalabilidade.

## Orquestração de recursos

A orquestração de recursos envolve o uso de ferramentas que coordenam e gerenciam automaticamente o provisionamento e a configuração de recursos de infraestrutura. Essas ferramentas podem lidar com tarefas complexas e sequenciais, como a criação de várias instâncias de servidores, configuração de redes, instalação de software e aplicação de políticas de segurança. Exemplos populares de ferramentas de orquestração incluem o Ansible, o Puppet e o Chef.

## O que é o Ansible

Ansible é uma ferramenta de automação e gerência de configuração, sem a necessidade de um centralizador e de agentes instalados nas máquinas. Podemos configurar centenas de máquinas de um ponto qualquer através de um arquivo de formato muito simples chamado YAML.

## Inicializando o ambiente

**Pre-requisitos**

- Vagrant (https://developer.hashicorp.com/vagrant/docs/installation)

- VirutalBox 6.1 (https://www.virtualbox.org/wiki/Download_Old_Builds_6_1)

- Git (https://git-scm.com/downloads)

**Inicializando  infraestrutura**

Nome       | vCPUs | Memoria RAM | IP            | S.O.¹           | Script de Provisionamento²
---------- |:-----:|:-----------:|:-------------:|:---------------:| -----------------------------
ansible-automation       | 2     | 2048MB      | 192.168.56.10 | ubuntu/focal64 | 
docker-host    | 2     | 2048MB        | 192.168.56.20 | ubuntu/focal64 | 


```git clone https://github.com/silvemerson/ansible-quinta-as-quinze.git```

```cd ansible-quinta-as-quinze/infra/```

```vagrant up```

## Ansible

### o que é Ansible Playbooks

Playbooks são tarefas de automação, que são ações de TI complexas executadas com pouca ou nenhuma intervenção humana. Ansible Playbooks são executados em um conjunto, grupo ou classificação de hosts que, juntos, formam um inventário do Ansible.


```
--- name: update web servers
  hosts: webservers
  remote_user: root

  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: update db servers
  hosts: databases
  remote_user: root

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

### O que são Tarefas(Tasks) no Ansible

As task são elementos dentro da playooks onde executamos ações para os grupo ou hosts destinos

Exemplo de Task:

```
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

```

### O que é o Ansible Inventory

É o arquivo de referência 

### 
- o que é o ansible galaxy
- o que é cada diretório daquele

### Instalando o Ansible

#### Removando versão antiga

```sudo apt remove ansible```

```sudo apt --purge autoremove```

```sudo apt update``` 

```sudo apt upgrade```

#### Instalando versão mais recente

```sudo apt update```

```sudo apt install software-properties-common```

```sudo apt-add-repository ppa:ansible/ansible```

```sudo apt install ansible```

```export ANSIBLE_HOST_KEY_CHECKING=False```

## Ansible Módulos

O Ansible possuí algo chamado de biblioteca de módulos onde podemos executar diretamente nos hosts através das playboooks ou comandos Ad Hoc
Também podemos escrever módulos personalizados. 

## Ansible Ad hoc

O Ad hoc do Ansible usa a ferramenta de linha de comando /usr/bin/ansible para automatizar uma única tarefa em um ou mais para hosts do inventário. 

exemplos de comandos ad hoc:

```ansible -i devops-heroes docker-host -m ping```

```ansible -i devops-heroes docker-host -m shell -a uptime```

```ansible -i devops-heroes docker-host -m shell -a free```

```ansible -i devops-heroes docker-host -m shell -a "service sshd status" ```


## Extra - Ansible Tower e AWX

O Ansible Tower é uma aplicação web desenvolvida para rodar em um servidor Linux, enquanto o Ansible AWX pode ser executado em qualquer plataforma que suporte Python. Além disso, o Ansible Tower requer um banco de dados relacional, como o MySQL ou o PostgreSQL, para armazenar os resultados das tarefas, enquanto o Ansible AWX não precisa de um banco de dados.

## Criando nossa Playbook

#### Instalação do Docker via Script

```
---
- hosts: docker-host
  become: yes
  become_user: vagrant
  become_method: sudo
  tasks:
    - name: Copiando get-docker.sh
      ansible.builtin.copy:
        src: files/get-docker.sh
        dest: /home/vagrant/get-docker.sh
        mode: '777'

    - name: Executa script
      command: sh /home/vagrant/get-docker.sh

    - name: Start Docker, if not started
      ansible.builtin.service:
        name: docker
        state: started
    
    - name: Add usuario ao grupo do docker
      ansible.builtin.user:
        name: vagrant
        group: docker
```

```ansible-playbook -i devops-heroes docker-script.yml```

#### Instalação do Docker via Pacote

```
---
- hosts: docker-host
  become: yes
  become_user: vagrant
  become_method: sudo
  tasks:
    - name: Instala pacotes que permitem que o apt seja usado sobre HTTPS
      apt:
        name: "{{ packages }}"
        state: present
        update_cache: yes
      vars:
        packages:
        - apt-transport-https
        - ca-certificates
        - gnupg-agent
        - software-properties-common

    - name: Adiciona uma chave de assinatura apt para o Docker
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Adiciona repositorio apt para versao estavel
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable
        state: present

    - name: Instala o Docker e suas dependencias
      apt:
        name: "{{ packages }}"
        state: present
        update_cache: yes
      vars:
        packages:
        - docker-ce
        - docker-ce-cli
        - containerd.io
      notify:
        - docker status

    - name: Permissao ao docker
      command: usermod -aG docker vagrant
```

```ansible-playbook -i devops-hero docker-apt.yml```

### Criando um container usando o módulo de Docker

```ansible-galaxy collection install community.docker```

```
---
- hosts: docker-host
  become: yes
  become_user: vagrant
  become_method: sudo
  tasks:
    - name: Criando app
      community.docker.docker_container:
        name: supermario
        image: pengbai/docker-supermario
        exposed_ports:
          - 8080
        ports:
          - "8600:8080"
         
```

```ansible-playbook -i devops-heroes deploy-container.yml -vv```