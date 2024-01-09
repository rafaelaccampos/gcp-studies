<h1 align="center">Estudos GCP</h1>

## 💻 Projeto
Esse repositório tem o objetivo de armazenar os estudos sobre Google Cloud Platform, especialmente sobre a certificação Cloud Associate Engineer.

## 🧪 Tópicos de estudos

### 00 - Fundamentos da Computação em Nuvem

![Computação em Nuvem](images/cloud-computing.svg)

**O que é Computação em Nuvem?**
- Computação em nuvem é a prática de alocar recursos computacionais através de provedores especializados, como GCP, AWS e Azure. Esses provedores oferecem a capacidade de provisionar rapidamente máquinas virtuais, proporcionando uma arquitetura que é tanto versionável quanto escalável. Esse provisionamento é facilitado por APIs de integração, permitindo que equipes de infraestrutura codifiquem e gerenciem recursos de forma eficiente.
- Além de simplificar a gestão e o provisionamento, a computação em nuvem possibilita escalabilidade. Isso significa que a capacidade de processamento pode ser ampliada sem a necessidade de aumentar a complexidade de uma única máquina. Ao invés disso, sistemas podem ser replicados em várias máquinas distintas. Para realizar esse processo, utiliza-se um balanceador de carga (load balancer), que distribui as requisições de forma equilibrada entre as diferentes máquinas. Dessa maneira, o sistema opera de maneira distribuída, garantindo eficiência e disponibilidade.


**Modelos de Serviço**

**Infraestrutura como Serviço (IaaS)** 
- Fornece acesso a recursos computacionais, como servidores virtuais, armazenamento e redes. Os usuários gerenciam os sistemas operacionais e as aplicações, enquanto o provedor de nuvem é responsável pela infraestrutura física.

Exemplos:
- AWS: Amazon EC2 (Simple Storage Service) para computação virtualizada, Amazon S3 (Simple Storage Service) para armazenamento de objetos, Amazon VPC (Virtual Private Cloud) para redes isoladas e Amazon RDS (Relational Database Service) para banco de dados relacionais.
- Microsoft Azure: Máquinas Virtuais para computação virtualizada, Azure Blob para armazenamento de objetos, Rede virtuais do Azure e banco de dados gerenciados, como o Azure SQL Database.
- Google Cloud Platform: Compute Engine para instâncias de máquinas virtuais, Cloud storage para armazenamento de objetos e Virtual Private Cloud (VPC) para redes privadas.

**Plataforma como Serviço (PaaS)**
- Oferece uma plataforma completa de desenvolvimento e implantação de aplicações. Os desenvolvedores podem criar, executar e gerenciar aplicações sem se preocupar com a infraestrutura subjacente.
Exemplos:
- Heroku: É uma plataforma de nuvem que permite aos desenvolvedores construir, executar e escalar padrões de forma rápida e fácil, sem se preocupar com a infraestrutura subjacente.
- Google App Engine - É uma plataforma PaaS que permite aos desenvolvedores criar e hospedar aplicações web e móveis de forma escalável gerenciada pelo Google.
- Microsoft Azure Services: Azure App Services é um serviço de PaaS da Microsoft que permite aos desenvolvedores construir, implantar e escalar aplicações WEB, móveis e API de forma rápida e eficente.

**Software como Serviço (SaaS)**
- Permite que os usuários acessem e utilizem aplicações hospededas na nuvem pela internet, geralmente atráves de um navegador WEB.
Ex: Serviços de e-mail, e colaboração online.
Exemplos:
- Salesforce - É uma plataforma de CRM (Customer Relationship Managament), baseada em nuvem que oferece uma variedade de aplicações empresariais.
- Google Workspace - É uma suite de aplicativos como Gmail, Google Drive, Google Docs, Google Sheets, Google Slides.
- Microsoft Office 365 - É uma suite de aplicações de produtividade baseada em nuvem da Microsoft que inclui Word, Excel, Powerpoint, Outlook, OneDrive, Teams e etc.

![Iaas, Paas, Saas](images/iaas-paas-saas.png)


**Modelos de implantação**

**Nuvem pública** 
- Recursos são disponibilizados ao público geral e operados por provedores de servidores de nuvem.

**Nuvem privada**
- Infraestrutura de nuvem é exclusivamente por uma única organização.

**Nuvem hibrída**
- Combina nuvens públicas e privadas, permitindo a transferência de dados e aplicações entre elas.

**Vantagens**
- Escabilidade
- Flexibilidade
- Redução de custos
- Alta disponibilidade
- Atualizações automáticas e acesso remoto

**Desvantagens**
- Questões de segurança
- Conformidade
- Dependência de provedores
- Gerenciamento de custos


### 1 - Fundamentos do Google Cloud Platform

**O que é?**
 - O Google Cloud Platform (GCP) é uma suite de serviços de computação em nuvem oferecidos pelo Google.

**Tipos de serviços de computação em nuvem**
- Compute resources
- Storage
- Networking
- Specialized services such as machine learning services

### 2 - Google Cloud Computing Services

### 3 - Projetos, Service Accounts e Billings

### 4 - Introdução a Computação em Google Cloud

### 5 - Computing com Compute Engine Virtual Machine

**Regiões e Zonas**
![Regiões e Zonas doc - resumir](https://cloud.google.com/compute/docs/regions-zones?hl=pt-br)

### 6 - Gerenciando Virtual Machines

### 7 - Computação com Kubernetes

### 8 - Gerenciando Cluster Kubernetes

### 9 - Computação com Cloud Run e App Engine

### 10 - Computação com Cloud Functions

### 11 - Planejando Storage na Cloud

### 12 - Deploying Storage no Google Cloud

### 13 - Carregando datas no Storage

### 14 - Network na Cloud: Virtual Private Clouds e Virtual Private Networks

### 15 - Network na Cloud: DNS, Load Balancing, Google Private Access e IP Addressing

### 16 - Deploying Applications com Cloud Marketplace e Cloud Foundation Toolkit

### 17 - Configurando acesso e segurança

### 18 - Monitorando, Logando e Estimando Custos

### 19 - Simulados

## 🚀 Materiais
- [Documentação do Google Cloud](https://cloud.google.com/docs?hl=pt-br)
- [Awesome GCP Certifications](https://github.com/sathishvj/awesome-gcp-certifications)
- [Alura](https://cursos.alura.com.br/course/google-cloud-engineer-certificacao-parte1)
- [Coursera](https://www.coursera.org/professional-certificates/cloud-engineering-gcp)
- [FreedCodeCamp](https://www.youtube.com/watch?v=jpno8FSqpc8&t=1435se)
- [Mapas mentais](https://thecloudgirl.dev/)
- [Vídeos GCP do Google](https://www.youtube.com/user/googlecloudplatform)
- [Livro oficial do Google](https://www.google.com.br/books/edition/Google_Cloud_Certified_Associate_Cloud_E/YcirEAAAQBAJ?hl=pt-BR&gbpv=1&dq=Official+Google+Cloud+Certified+Associate+Cloud+Engineer&printsec=frontcover)
- [ExamsTopics](https://www.examtopics.com/exams/google/associate-cloud-engineer/?source=post_page-----4af23a742b49--------------------------------)
- [Udemy](https://www.udemy.com/course/google-cloud-certification-associate-cloud-engineer/)