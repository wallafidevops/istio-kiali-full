# 🌐 Instalação do Istio com Kiali no EKS

Este guia descreve o processo de instalação do **Istio**, configuração de **Gateways e Ingress**, instalação do **Kiali** e deploy de aplicações de exemplo no cluster Kubernetes (EKS).

---

## 📖 O que é o Istio?

O **Istio** é uma **Service Mesh** que provê observabilidade, segurança e controle de tráfego em aplicações baseadas em microsserviços no Kubernetes. Ele permite gerenciar comunicações entre serviços com **tráfego inteligente, políticas de segurança e monitoramento detalhado**.

---

## 📌 Componentes principais

- **Gateway** → Recurso do Istio que expõe serviços para fora do cluster Kubernetes, controlando tráfego de entrada/saída.  
- **VirtualService** → Define regras de roteamento para o tráfego dentro do cluster (ex.: rotear requisições para versões diferentes de um serviço).  
- **DestinationRule** → Especifica políticas de tráfego para serviços (como balanceamento, conexões TLS, failover).  
- **Kiali** → Ferramenta de observabilidade para o Istio, permitindo visualizar malhas de serviço, métricas e fluxos de tráfego de forma gráfica.  

---

## 🚀 Instalação do Istio

### 1. Acessar o diretório do Istio
```bash
cd istio-1.26.3/
export PATH=$PWD/bin:$PATH
```

### 2. Instalar o Istio com configuração customizada
```bash
istioctl install -f istiod-operator-prod.yml -y
```

---

## 🌍 Aplicando Gateway e Ingress

### 1. Criar o Gateway
```bash
kubectl apply -f gateway.yml
```

### 2. Criar o Ingress
```bash
kubectl apply -f ingress.yml
```

---

## 📊 Instalando Kiali e Addons

⚠️ **Importante**: antes de instalar o Kiali, certifique-se de que a **Stack de Monitoramento** já está instalada.  
👉 Repositório: [Monitoring EKS Full](https://github.com/wallafidevops/monitoring-eks-full)

### 1. Entrar no diretório de addons
```bash
cd istio-1.26.3/samples/addons/affinity
```

### 2. Configurar integração com o Grafana
No arquivo `kiali.yaml`, **linha 124**, adicione a URL do Grafana.

### 3. Aplicar addons (Kiali, Prometheus, Grafana, etc.)
```bash
kubectl apply -f .
```

### 4. Aplicar VirtualService do Kiali
```bash
cd istio-1.26.3/samples/addons/affinity/vs
kubectl apply -f vs.yaml
```

---

## 🛠️ Deploy de Aplicações

### 1. Criar namespace com injeção automática do Istio
```bash
kubectl create namespace giropops
kubectl label namespace giropops istio-injection=enabled
```

### 2. Subir serviços e regras de tráfego
```bash
cd /istio-kiali-full/aplication

kubectl apply -f chaos-deployment.yaml
kubectl apply -f chaos-service.yaml
kubectl apply -f giropops-deployment.yaml
kubectl apply -f giropops-service.yaml
kubectl apply -f destinationrule-giropops-v1.yaml
kubectl apply -f virtualservice-giropops-v1.yaml
```

---

## 🔑 Segurança

Para funcionamento correto, abra as portas **15017** e **15021** no **Security Group** dos nós do cluster EKS.  

---

## ❌ Desinstalação

### 1. Voltar ao diretório do Istio
```bash
cd istio-1.26.3/
export PATH=$PWD/bin:$PATH
```

### 2. Remover todos os recursos do Istio
```bash
istioctl uninstall --purge -y
```

---

## 📜 Resumo

- **Istio**: Service Mesh para observabilidade, segurança e tráfego inteligente.  
- **Gateway**: expõe serviços para fora do cluster.  
- **VirtualService**: define regras de roteamento do tráfego.  
- **DestinationRule**: políticas para controle de tráfego.  
- **Kiali**: visualização gráfica da malha de serviços.  

---

👉 Esse guia cobre desde a instalação até a remoção do Istio com Kiali em um cluster Kubernetes no EKS.
