# Hands on | Criação de um Ambiente de Desenvolvimento para Engenharia de Dados com Kubernetes

## Overview

Este projeto demonstra como configurar um ambiente de desenvolvimento para engenharia de dados utilizando Kubernetes. Ele abrange a instalação e configuração do Kubernetes, a implementação de ferramentas essenciais como Apache Spark e Jupyter, e as práticas recomendadas para orquestração de contêineres e gerenciamento de recursos. Além disso, o projeto inclui um Dockerfile para facilitar a implantação de contêineres Docker e scripts de configuração para automatizar a criação e gerenciamento do ambiente.

## Tabela de Conteúdo

- [Objetivo](#objetivo)
- [Fluxo de Versionamento](#fluxo-de-versionamento)
- [Ferramentas](#ferramentas)
- [Getting Started](#getting-started)
- [Requisitos](#requisitos)
- [Usage](#usage)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Troubleshooting](#troubleshooting)
- [Contribuições](#contribuições)
- [License](#license)
- [Contato](#contato)
- [Acknowledgments](#acknowledgments)

## Objetivo

O objetivo deste projeto é criar um ambiente de desenvolvimento escalável e eficiente para engenharia de dados usando Kubernetes. Este ambiente inclui a configuração de um cluster Kubernetes minikube, integração de ferramentas de análise de dados como Apache Spark, Airflow e Jupyter Notebook, e a implementação de pipelines de dados automatizados. O projeto visa facilitar o processamento e análise de grandes volumes de dados, garantindo flexibilidade, eficiência e segurança em ambiente local.

# Fluxo de Versionamento

Este projeto segue um fluxo de versionamento baseado no [gitflow](https://www.atlassian.com/br/git/tutorials/comparing-workflows/gitflow-workflow). Cada grande alteração no projeto deve ser acompanhada por um novo commit com uma mensagem descritiva. Versões dos componentes e das configurações devem ser controladas com base em tags no Git.

# Ferramentas

Abaixo segue o que foi utilizado na criação deste projeto:

* [Minikube](https://minikube.sigs.k8s.io/docs/start/) - Ferramenta de código aberto que permite criar um ambiente de teste do Kubernetes em sua máquina local. Com o Minikube, é possível criar e implantar aplicativos em um cluster Kubernetes em sua máquina local.
* [Helm](https://helm.sh/) - Ferramenta de gerenciamento de pacotes de código aberto para o Kubernetes. O Helm permite empacotar aplicativos Kubernetes em um formato padrão chamado de gráfico, que inclui todos os recursos necessários para implantar o aplicativo, incluindo configurações e dependências.
* [Spark](https://spark.apache.org/) - O Spark é um framework de processamento de dados distribuído e de código aberto, que permite executar processamento de dados em larga escala, incluindo processamento em batch, streaming, SQL, machine learning e processamento de gráficos. Ele foi projetado para ser executado em clusters de computadores e fornece uma interface de programação fácil de usar para desenvolvedores; 
* [Airflow](https://airflow.apache.org/) - O Airflow é uma plataforma de orquestração de fluxo de trabalho de dados de código aberto que permite criar, agendar e monitorar fluxos de trabalho complexos de processamento de dados. Ele usa uma linguagem de definição de fluxo de trabalho baseada em Python e possui uma ampla gama de conectores pré-construídos para trabalhar com diferentes sistemas de armazenamento de dados, bancos de dados e ferramentas de processamento de dados; 
* [Reflector](https://github.com/emberstack/kubernetes-reflector) - O Reflector é uma ferramenta de sincronização de estado de código aberto que permite sincronizar recursos Kubernetes em diferentes clusters ou namespaces. Ele usa a abordagem de controlador de reconciliação para monitorar e atualizar automaticamente o estado dos recursos Kubernetes com base em um estado desejado especificado; 
* [Minio](https://min.io/) - O Minio é um sistema de armazenamento de objetos de código aberto e de alta performance, compatível com a API Amazon S3. Ele é projetado para ser executado em clusters distribuídos e escaláveis e fornece recursos avançados de segurança e gerenciamento de dados; 
* [Postgres](https://www.postgresql.org/) - O Postgres é um sistema de gerenciamento de banco de dados relacional de código aberto, conhecido por sua confiabilidade, escalabilidade e recursos avançados de segurança. Ele é compatível com SQL e é usado em uma ampla gama de aplicativos, desde pequenos sites até grandes empresas e organizações governamentais.

<!-- GETTING STARTED -->

# Começando

Siga estas instruções para configurar e utilizar os códigos deste repositório em seu ambiente local. 

Este guia apresentará uma série de comandos para instalar as ferramentas necessárias.

## Pré-requisitos

Antes de começar, certifique-se de ter os seguintes pré-requisitos instalados no seu sistema:

1. Minikube:
    
    Certifique-se de ter o Minikube instalado e em execução no seu ambiente de desenvolvimento.
    - Instalação:  Visite o site do [Minikube](https://minikube.sigs.k8s.io/docs/start/). e siga as instruções do seu sistema operacional.

2. Docker:
  
    Certifique-se de ter o Minikube instalado e em execução no seu ambiente de desenvolvimento.

    - Instalação: Instale o Docker seguindo as instruções do seu sistema operacional no [site do Docker](https://docs.docker.com/get-docker/).

3. Kubernetes CLI (kubectl):
    - Instalação: Instale `kubectl` seguindo as instruções do seu sistema operacional no [site do Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
4. Helm:
    
    Versão 3.x ou superior
    
    - Instalação: Instale o Helm seguindo as instruções para seu sistema operacional no [site do Helm](https://helm.sh/docs/intro/install/).

## instalação do cluster

O primeiro passo é montar um ambiente com um cluster Kubernetes local para executar a aplicação e o pipeline de dados. Para este projeto, usaremos o cluster de Kubernetes **[minikube](https://minikube.sigs.k8s.io/docs/)**. [Siga este guia de instalação para instalar o Minikube](https://minikube.sigs.k8s.io/docs/start/).

Também usaremos o **[helm](https://helm.sh/)** para nos ajudar a instalar algumas aplicações. [Siga este guia de instalação para instalar o Helm](https://helm.sh/docs/intro/install/).

Após instalar esses pré-requisitos, é hora de iniciar o nosso cluster Minikube. Para que tudo ocorra bem, é aconselhável usar um cluster de no mínimo 8GB de memória e 2 CPUs. Execute o seguinte comando no terminal:

```
minikube start --memory=8000 --cpus=2
```

Para acessar alguns serviços via loadbalancer ou ingress no Minikube, é necessário utilizar o [tunelamento do minikube](https://minikube.sigs.k8s.io/docs/handbook/accessing/#example-of-loadbalancer) e habilitar o [ingress](https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns/#Linux).

Para habilitar o ingress execute:

```sh
minikube addons enable ingress
```

Apos o comando acima ser executado o seu minikube estará com o ingress habilitado pronto para uso, e para ter acesso tanto a ingress quanto para o loadbalancer, abra uma nova aba no seu terminal e execute o seguinte comando:

```sh
minikube tunnel
```

Com isso, o seu cluster estará pronto para receber acessos via loadbalancer e ingress.

### Instalação das ferramentas

Depois do ambiente inicializado será necessario instalar algumas aplicações que serão responsaveis por manter e gerenciar nosso pipeline de dados.

Estando conectado em um cluster Kubernetes, execute os seguintes comandos para criar todos os namespaces necessarios:

```sh
kubectl create namespace orchestrator
kubectl create namespace database
kubectl create namespace ingestion
kubectl create namespace processing
kubectl create namespace datastore
kubectl create namespace deepstorage
kubectl create namespace app
kubectl create namespace management
kubectl create namespace misc
```
Agora vamos adicionar todos os repositorios necessarios para a instalação as demais ferramentas do nosso projeto, executando os seguintes comandos:

```sh
# add & update helm list repos
helm repo add emberstack https://emberstack.github.io/helm-charts
helm repo add minio https://charts.min.io/
helm repo add apache-airflow https://airflow.apache.org/
helm repo add jupyterhub https://hub.jupyter.org/helm-chart/
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add spark-operator https://kubeflow.github.io/spark-operator
helm repo add strimzi https://strimzi.io/charts/
helm repo add confluentinc https://confluentinc.github.io/cp-helm-charts/
helm repo update
```

Uma vez que adicionamos os repositorios podemos agora fazer a instalação diretamente no nosso cluster.
Vamos instalar todas as ferramentas da nossa stack de engenharia de dados.

É hora de adicionar as ferramentas necessárias para o pipeline de dados. Para isso, é preciso criar secrets para armazenar senhas e informações confidenciais acessíveis pelas aplicações e processos do Spark. É necessário que eles estejam no namespace onde a aplicação está rodando e também no namespace processing, onde serão executados os processos do spark. Para realizar essa replicação dos secrets em diferentes namespaces, podemos usar o **[Reflactor](https://github.com/EmberStack/kubernetes-reflector)**, que evita a necessidade de recriar os secrets em namespaces diferentes. Para utilizá-lo, basta executar o comando a seguir:

```sh
helm upgrade --cleanup-on-fail \
  --install reflector emberstack/reflector \
  --namespace management \
  --version 7.0.151
```

> Antes de executar os comandos, você pode alterar os secrets dos arquivos localizados na pasta `secrets/` se quiser mudar as senhas de acesso aos bancos de dados e ao storage.

Após o Reflector estar funcionando, execute o comando que cria os secrets nos namespaces necessários:

```sh
# secrets
kubectl apply -f secrets/minio-secrets.yaml
kubectl apply -f secrets/postgres-secrets.yaml
```

> Caso não queira instalar o Reflactor para automatizar o processo de criar o secret em vários namespaces diferentes, você pode replicar manualmente o secret para outro namespace executando este comando:
`kubectl get secret minio-secrets -n deepstorage -o yaml | sed s/"namespace: deepstorage"/"namespace: processing"/| kubectl apply -n processing -f -`

Uma vez que os secrets estejam configurados, é possível instalar os bancos de dados e o storage do pipeline de dados com o seguinte comando:

```sh
helm upgrade --cleanup-on-fail \
  --install minio minio/minio \
  --values manifests/deepstorage/minio.yaml \
  --namespace deepstorage \
  --version 5.0.13
```
Para acessar o serviço do minio via LoadBalancer basta executar:

```sh
echo http://$(kubectl get svc --namespace deepstorage minio-console --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}"):9001
```
Para verificar o usuario e password do minio:
```sh
echo Usuario:$(kubectl get secret minio-secrets --namespace deepstorage -o jsonpath="{.data.rootUser}" | base64 --decode) - Password:$(kubectl get secret minio-secrets --namespace deepstorage -o jsonpath="{.data.rootPassword}" | base64 --decode)
```

Depois vamos instalar o banco de dados na camada de database:

```sh
helm upgrade --cleanup-on-fail \
  --install postgresql oci://registry-1.docker.io/bitnamicharts/postgresql \
  --values manifests/database/postgresql.yaml \
  --namespace database \
  --version 12.2.4
```

Para saber o IP LoadBalancer do serviço do postgres execute:

```sh
echo $(kubectl get svc --namespace database postgresql --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
```

Para verificar o password do postgres:
```sh
echo $(kubectl get secret postgres-secrets --namespace database -o jsonpath="{.data.password}" | base64 --decode)
```

Por fim, instale o Spark, o Airflow e o jupyterhub, juntamente com suas permissões para executar os processos do Spark, executando os seguintes comandos:

```sh
# processing
helm upgrade --cleanup-on-fail \
  --install spark spark-operator/spark-operator \
  --values manifests/processing/spark.yaml \
  --namespace processing \
  --version 1.4.2
```

Antes de instalar o Airflow, é preciso atender a um requisito: criar um secret contendo sua `chave ssh`, para que o Airflow possa baixar as `DAGs` necessárias por meio do `gitSync`. É possível criar esse secret com o seguinte comando:

> Lembrando que para este comando funcionar é necessario que você tenha uma `chave ssh` configurada na sua maquina para se conectar com o github no seu computador. Caso não tenha, use [este guia](https://docs.github.com/pt/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) para criar uma e [adiciona-la ao github](https://docs.github.com/pt/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).

```sh
kubectl create secret generic airflow-ssh-secret --from-file=gitSshKey=$HOME/.ssh/id_ed25519 -n orchestrator
```

```sh
# orchestrator
helm upgrade --cleanup-on-fail \
  --install airflow apache-airflow/airflow \
  --values manifests/orchestrator/airflow.yaml \
  --namespace orchestrator \
  --version 1.8.0
```

Em seguida, instale as configurações de acesso:

```sh
kubectl apply -f access-control/crb-spark-operator-airflow-orchestrator.yaml
kubectl apply -f access-control/crb-spark-operator-airflow-processing.yaml
```

Para que seja possivel o Ariflow executar de maneira independente os processos spark é preciso que ele tenha uma conexão com o cluster, e para isto é necessario passar essa informação ao Airflow. Para adicionar a conexão com o cluster ao Airflow basta criar as conexões necessarias na UI do Airflow, ou caso deseje fazer isso de forma aotomatizada execute:

```sh
kubectl get pods --no-headers -o custom-columns=":metadata.name" -n orchestrator | grep scheduler | xargs -i sh -c 'kubectl cp connections.json orchestrator/{}:./ -c scheduler | kubectl -n orchestrator exec {} -- airflow connections import connections.json'
```
<!-- export SCHEDULER_POD_NAME="$(kubectl get pods --no-headers -o custom-columns=":metadata.name" -n orchestrator | grep scheduler)"
kubectl cp connections.json orchestrator/$SCHEDULER_POD_NAME:./ -c scheduler
kubectl -n orchestrator exec $SCHEDULER_POD_NAME -- airflow connections import connections.json -->

Agora vamos instalar o Jupyterhub com o seguinte comando:

```sh
helm upgrade --cleanup-on-fail \
  --install kupyterhub jupyterhub/jupyterhub \
  --values manifests/processing/jupyterhub.yaml \
  --namespace processing \
  --version 3.3.7
```

Para acessar o jupyterhub execute:

```sh
echo http://$(kubectl get service proxy-public --namespace processing --output jsonpath='{.status.loadBalancer.ingress[].ip}')
```

Ótimo, agora que você configurou as ferramentas necessárias, temos o ambiente de desenvolvimento e de execução instalado e pronto para uso.

### Executando o projeto

Certifique-se de testar e validar seus pipelines de dados antes de implantá-los em produção. Isso ajudará a garantir que seus processos estejam funcionando corretamente e que os dados estejam sendo tratados de maneira apropriada.

## Getting Started

Siga estas instruções para configurar o ambiente de desenvolvimento e executar o projeto:

1. Clone o repositório:
```sh
git clone https://github.com/seu-usuario/kubernetes-data-engineering-env.git
cd kubernetes-data-engineering-env
```

2. Instale as dependências e configure o Kubernetes:
```sh
kubectl apply -f k8s/config/
```

3. Implante os componentes principais:
```sh
helm install spark k8s/helm/spark
helm install jupyter k8s/helm/jupyter
```

4. Acesse o Jupyter Notebook:
Acesse o serviço Jupyter em seu navegador:
```sh
kubectl port-forward svc/jupyter 8888:8888
```
Abra `http://localhost:8888` e insira a token fornecida.

## Requisitos

- Kubernetes 1.18+
- Helm 3+
- Docker
- Kubectl

## Usage

Após seguir as instruções em **[Getting Started](#getting-started)**, você pode utilizar o Jupyter Notebook para desenvolver e executar scripts de análise de dados. Apache Spark está disponível para processamento distribuído de grandes volumes de dados.

## Dockernization

Este projeto pode ser facilmente "dockerizado" para facilitar a implantação em contêineres Docker. A utilização de contêineres Docker ajuda a garantir a portabilidade e a independência das dependências do sistema, tornando a implantação mais fácil e consistente em diferentes ambientes.

Siga estas etapas para criar uma imagem Docker e executar a aplicação em um contêiner:

### 1. Construir uma imagem Docker do Jupyter Notebook

```sh
docker build -t data-env-jupyter:latest -f Dockerfile.jupyter .
```

### 2. Executar o contêiner

```sh
docker run -d -p 8888:8888 --name jupyter data-env-jupyter:latest
```
O comando acima executa um contêiner Docker a partir da imagem que você construiu. Ele mapeia a porta 8888 do contêiner para a porta 8888 do host local.

### 3. Acesse o Jupyter Notebook

Abra `http://localhost:8888` e insira a token fornecida.

2. Verifique os resultados dos testes e analise possíveis falhas para garantir que todos os componentes estejam funcionando corretamente.

## Estrutura do Projeto

O projeto tem a seguinte estrutura de diretórios:

```
.
├── docker
│   ├── Dockerfile.jupyter
│   └── Dockerfile.spark
├── k8s
│   ├── config
│   │   ├── jupyter.yaml
│   │   └── spark.yaml
│   └── helm
│       ├── jupyter
│       │   └── Chart.yaml
│       └── spark
│           └── Chart.yaml
├── scripts
│   ├── data_pipeline.py
│   └── setup_env.sh
├── tests
│   ├── test_jupyter.py
│   └── test_spark.py
├── README.md
└── LICENSE

```

## Troubleshooting

Se você encontrar problemas ou tiver dúvidas sobre o projeto, consulte a seção **[Contribuições](#contribuições)** para obter informações sobre como relatar problemas ou fazer perguntas.

## Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para criar uma solicitação pull com melhorias, correções de bugs ou novos recursos. As contribuições são o que tornam a comunidade de código aberto um lugar incrível para aprender, inspirar e criar. Qualquer contribuição que você fizer será muito apreciada.

Para contribuir com o projeto, siga os passos abaixo:

1. Bifurque o projeto.
2. Crie um branch para sua contribuição (`git checkout -b feature-mycontribution`).
3. Faça as alterações desejadas no código.
4. Confirme suas alterações (`git commit -m 'MyContribution: Adicionando novo recurso'`).
5. Envie o branch para seu repositório Fork (`git push origin feature-mycontribution`).
6. Abra uma solicitação pull no branch principal do projeto original. Descreva as alterações e aguarde a revisão e discussão da comunidade.

Valorizamos verdadeiramente o seu interesse em contribuir para o projeto. Juntos, podemos torná-lo ainda melhor!

## License

Este projeto está licenciado sob a [MIT License](LICENSE).

## Contato

Para entrar em contato, envie um e-mail para [seuemail@example.com](mailto:seuemail@example.com).

<p align="center">

 <a href="https://twitter.com/seutwitter" target="_blank" >
     <img alt="Twitter" src="https://img.shields.io/badge/-Twitter-9cf?logo=Twitter&logoColor=white"></a>

  <a href="https://instagram.com/seuinstagram" target="_blank" >
    <img alt="Instagram" src="https://img.shields.io/badge/-Instagram-ff2b8e?logo=Instagram&logoColor=white"></a>

  <a href="https://www.linkedin.com/in/seulinkedin/" target="_blank" >
    <img alt="Linkedin" src="https://img.shields.io/badge/-Linkedin-blue?logo=Linkedin&logoColor=white"></a>

  <a href="https://t.me/seutelegram" target="_blank" >
    <img alt="Telegram" src="https://img.shields.io/badge/-Telegram-blue?logo=Telegram&logoColor=white"></a>

  <a href="mailto:seuemail@example.com" target="_blank" >
    <img alt="Email" src="https://img.shields.io/badge/-Email-c14438?logo=Gmail&logoColor=white"></a>

</p>

## Acknowledgments

Agradecimentos a todos os desenvolvedores e mantenedores das bibliotecas e ferramentas utilizadas neste projeto, bem como a toda a comunidade de código aberto que torna projetos como este possíveis.