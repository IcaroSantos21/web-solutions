# Web Solutions — Web Hosting Platform

Plataforma de hospedagem web desenvolvida para estudar e demonstrar a implantação de aplicações estáticas utilizando **Docker, Kubernetes, Nginx, Apache e Tailwind CSS**.

O projeto disponibiliza duas aplicações web independentes, executadas em contêineres e acessíveis por meio de rotas distintas em um Ingress do Kubernetes.

## Tecnologias utilizadas

- **Docker:** construção e empacotamento das imagens.
- **Kubernetes:** gerenciamento dos Deployments e Services.
- **k3d:** execução de um cluster Kubernetes local.
- **Traefik:** roteamento das requisições HTTP por Ingress.
- **Nginx:** servidor web para conteúdo estático.
- **Apache HTTP Server:** servidor web para conteúdo estático.
- **Tailwind CSS:** estilização das páginas HTML.
- **Node.js e npm:** gerenciamento de dependências e compilação do CSS.

## Arquitetura

O projeto utiliza dois servidores web independentes, cada um com sua própria imagem Docker, Deployment e Service no Kubernetes.

```text
                     Cliente HTTP
                          |
                          v
                   Traefik Ingress
                          |
               +----------+----------+
               |                     |
               v                     v
          /nginx/                 /apache/
               |                     |
               v                     v
       Strip Prefix             Strip Prefix
               |                     |
               v                     v
       Nginx Service           Apache Service
               |                     |
               v                     v
       Nginx Pods              Apache Pods
       (2 réplicas)            (2 réplicas)
```

O Ingress direciona as requisições para o servidor correspondente. Os middlewares removem os prefixos `/nginx` e `/apache` antes de encaminhar as requisições aos servidores web.

## Estrutura do projeto

```text
web-solutions-k8s/
├── docker/
│   ├── nginx/
│   │   ├── Dockerfile
│   │   ├── index.html
│   │   └── styles.css
│   └── apache/
│       ├── Dockerfile
│       ├── index.html
│       └── styles.css
├── kubernetes/
│   ├── nginx.yaml
│   ├── apache.yaml
│   ├── ingress.yaml
│   └── middlewares.yaml
├── styles/
│   ├── nginx.css
│   └── apache.css
├── package.json
├── package-lock.json
└── README.md
```

## Pré-requisitos

- Docker
- Node.js e npm
- kubectl
- k3d
- Cluster Kubernetes configurado com Traefik e acesso pela porta local `8080`

## Instalação e execução

### 1. Instalar as dependências

Na raiz do projeto, execute:

```bash
npm install
```

### 2. Compilar o Tailwind CSS

Gere os arquivos CSS utilizados pelas duas aplicações:

```bash
npm run build:css
```

Os arquivos gerados são:

- `docker/nginx/styles.css`
- `docker/apache/styles.css`

Execute essa etapa novamente sempre que alterar os arquivos de entrada do Tailwind e quiser atualizar o CSS de produção.

Para acompanhar alterações automaticamente durante o desenvolvimento, utilize os comandos em terminais separados:

```bash
npm run dev:nginx
```

```bash
npm run dev:apache
```

### 3. Construir as imagens Docker

Após compilar o CSS, construa as imagens:

```bash
docker build -t web-solutions-nginx:1.0 ./docker/nginx
```

```bash
docker build -t web-solutions-apache:1.0 ./docker/apache
```

### 4. Implantar no Kubernetes

Com o cluster existente em execução, aplique os manifests:

```bash
kubectl apply -f kubernetes/
```

Esse comando aplica os recursos YAML da pasta `kubernetes/`. Como o cluster também pode hospedar outras aplicações, não é necessário recriá-lo para implantar este projeto.

Verifique os Pods e Services:

```bash
kubectl get pods
kubectl get services
kubectl get ingress
```

Para consultar o estado dos Deployments:

```bash
kubectl rollout status deployment/nginx
kubectl rollout status deployment/apache
```

Os nomes dos recursos precisam corresponder aos definidos nos manifests.

## Acessar as aplicações

Com o Ingress configurado para a porta local `8080`, acesse:

| Aplicação | URL |
|---|---|
| Nginx | http://localhost:8080/nginx/ |
| Apache | http://localhost:8080/apache/ |

Os arquivos CSS também são servidos pelos respectivos servidores:

- http://localhost:8080/nginx/styles.css
- http://localhost:8080/apache/styles.css

## Testes e validação

Utilize `curl` para verificar as respostas HTTP:

```bash
curl -I http://localhost:8080/nginx/
curl -I http://localhost:8080/nginx/styles.css
curl -I http://localhost:8080/apache/
curl -I http://localhost:8080/apache/styles.css
```

As quatro rotas foram validadas com respostas `HTTP 200 OK`, indicando que as páginas e os arquivos CSS estavam acessíveis durante os testes.

Para investigar problemas nos recursos do Kubernetes:

```bash
kubectl get pods
kubectl get services
kubectl get ingress
kubectl logs deployment/nginx
kubectl logs deployment/apache
```

## Objetivos acadêmicos

Este projeto tem como objetivo praticar:

- Construção e execução de imagens Docker.
- Hospedagem de páginas estáticas com Nginx e Apache.
- Compilação e integração do Tailwind CSS.
- Criação de Deployments e Services no Kubernetes.
- Exposição de aplicações por meio de Ingress.
- Configuração de middlewares para manipulação de caminhos.
- Verificação de disponibilidade e diagnóstico básico de aplicações conteinerizadas.

## Licença

Projeto desenvolvido para fins acadêmicos e de aprendizado.
