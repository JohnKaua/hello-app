# Documentação hello-app

## Visão geral

Aplicação exemplo desenvolvida com **FastAPI**, configurada para ser **containerizada com Docker** e publicada automaticamente via **GitHub Actions** no **Docker Hub**.  
Além disso, o repositório contém um pipeline de **CI/CD** que atualiza os manifests do Kubernetes em outro repositório (`hello-manifests`), permitindo a automação completa via ArgoCD.

## Tecnologias utilizadas

- **FastAPI** – Framework Python para APIs
- **Docker** – Containerização da aplicação
- **GitHub Actions** – CI/CD
- **Docker Hub** – Repositório de imagens
- **ArgoCD** – CD via GitOps
- **Kubernetes (Rancher Desktop)** – Orquestração

## Fluxo CI/CD

### Desencadeamento

O pipeline roda automaticamente quando:

- há um **push na branch `main`**, ou
- o workflow é executado manualmente via **Actions → Run workflow**.

### Etapas do pipeline

- **Checkout source**:  Clona o código da aplicação
- **Login to DockerHub**:  Autentica com as credenciais do Docker Hub via secrets
- **Build and push Docker image**:  Constrói e publica a imagem no Docker Hub com duas tags: `latest` e `commit_sha`
- **Checkout manifests repository**:  Clona o repositório `hello-manifests`
- **Update image tag in manifests**:  Atualiza o campo `image:` no `deployment.yaml` com a nova tag publicada
- **Create Pull Request**:  Cria um PR automático no repositório `hello-manifests` com a atualização da imagem

## Secrets necessários

- `DOCKER_USERNAME`:  Usuário do Docker Hub
- `DOCKER_PASSWORD`:  Token de acesso do Docker Hub
- `MANIFESTS_REPO_TOKEN`:  Personal Access Token (Fine-grained) com acesso `Read/Write` aos repositórios `hello-manifests` e `hello-app`

## Execução manual

Na aba **Actions**, selecione o workflow **“CI/CD - Build & Update Manifests”** e clique em **“Run workflow”**.  
O processo criará:

- uma nova imagem no Docker Hub (`hello-app`);
- um Pull Request no `hello-manifests` atualizando a imagem.

## Resultado final

- Docker image disponível em: `docker.io/SEU-USERNAME/hello-app`
- Manifest atualizado no repositório `hello-manifests`
- Implantação automatizada via ArgoCD no Kubernetes