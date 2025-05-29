# Prova DevOps: Avaliação Prática 1

**Disciplina:** Fundamentos de DevOps
**Data da Atividade:** 29/05/2025 08:20–09:40

## Contexto da Atividade

Nesta avaliação prática, o objetivo é fazer um projeto simples em FastAPI com três testes automatizados. O objetivo é aplicar conhecimentos fundamentais de DevOps, contemplando:

- **Controle de versão:** Git e GitHub
- **Testes automatizados:** pytest
- **Commits semânticos** (Conventional Commits)
- **Integração e entrega contínua (CI/CD):** GitHub Actions
- **Containerização:** Docker e DockerHub
- **Orquestração local:** Kubernetes com Kind
- **Configuração declarativa:** Kustomize

## Estrutura do Repositório

```
prova-devops/
├── .github/workflows/        # GitHub Actions
│   ├── ci.yml               # CI: testes, build e push Docker
│   └── cd.yml               # CD: atualização de kustomize
├── app/                     # Aplicação FastAPI
│   ├── main.py              # Aplicação FastAPI
│   ├── test_main.py         # Testes pytest
│   └── requirements.txt     # Dependências Python
├── k8s/                     # Manifests Kubernetes (Kustomize)
│   ├── deployment.yaml      # Deployment da aplicação
│   ├── service.yaml         # Service para exposição
│   └── kustomization.yaml   # Configuração do Kustomize
├── Dockerfile               # Imagem da aplicação FastAPI
├── kind.yaml                # Configuração do cluster Kind
└── README.md                # Este arquivo
```

## Pré-requisitos

- **Git** (>=2.25)
- **Python** (>=3.8)
- **pip**
- **virtualenv** ou venv nativo
- **Docker**
- **kubectl**
- **Kind**
- **Kustomize**

## Como Rodar Localmente

### 1. Clonar o Repositório

```bash
git clone https://github.com/gabriel04alves/prova-devops.git
cd prova-devops
```

### 2. Criar e Ativar o Ambiente Virtual

```bash
python -m venv .venv
source .venv/bin/activate    # Linux/macOS
.\.venv\Scripts\activate   # Windows
```

### 3. Instalar Dependências

```bash
pip install --upgrade pip
pip install -r app/requirements.txt
```

### 4. Executar Testes

```bash
cd app
pytest
```

### 5. Iniciar a Aplicação

```bash
cd app
python main.py
```

Ou usando uvicorn:

```bash
cd app
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Acesse [http://localhost:8000](http://localhost:8000)

#### Endpoints disponíveis

- `GET /` → `{ "message": "Hello World!!!" }`
- `GET /square/{x}` → `{ "result": x * x }`
- `GET /double/{x}` → `{ "result": x * 2 }`

## Containerização com Docker

1. Build da imagem:

   ```bash
   docker build -t gabriel04alves/devops-prova:latest .
   ```

2. Executar o container:

   ```bash
   docker run -p 8000:8000 gabriel04alves/devops-prova:latest
   ```

3. Push para o DockerHub:
   ```bash
   docker push gabriel04alves/devops-prova:latest
   ```

## Kubernetes com Kind e Kustomize

### 1. Criar cluster Kind

```bash
kind create cluster --name devops --config kind.yaml
```

### 2. Deploy com Kustomize

```bash
kubectl apply -k k8s/
```

### 3. Testar via port-forward

```bash
kubectl port-forward svc/devops-prova-service 8000:8000
```

Acesse [http://localhost:8000](http://localhost:8000)

## CI/CD com GitHub Actions

### Workflow de CI (`.github/workflows/ci.yml`)

Executa a cada push:

1. Configuração do Python 3.13
2. Instalação das dependências
3. Execução dos testes (`pytest`)
4. Build e push da imagem Docker para o DockerHub

### Workflow de CD (`.github/workflows/cd.yml`)

Executa a cada push na branch `main`:

1. Atualização do `kustomization.yaml` com nova tag de imagem
2. Commit automático da mudança
3. Push das alterações

## Configuração de Secrets

Para o funcionamento do CI/CD, configure os seguintes secrets no GitHub:

- `DOCKERHUB_USERNAME`: Seu usuário do DockerHub
- `DOCKERHUB_TOKEN`: Token de acesso do DockerHub
