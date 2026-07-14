# postech-fase-5-gitops

Repositório GitOps da SolidaryTech. Contém manifests Kubernetes e Applications do ArgoCD para os microsserviços.

## Estrutura

```text
argocd-apps/          # Applications do ArgoCD (aplicar uma vez no cluster)
ngo-service/          # Manifests do NGO Service
donation-service/     # Manifests do Donation Service
volunteer-service/    # Manifests do Volunteer Service
```

## Pré-requisitos

1. Cluster EKS provisionado via Terraform (`resources/`)
2. ArgoCD instalado no cluster
3. Namespace `solidarytech` (criado automaticamente pelo ArgoCD)
4. Secrets criados manualmente no cluster (não commitados):

```bash
kubectl create namespace solidarytech

kubectl create secret generic ngo-service-secrets \
  --namespace solidarytech \
  --from-literal=DATABASE_URL="postgres://USER:PASS@HOST:5432/ngo_db"

kubectl create secret generic donation-service-secrets \
  --namespace solidarytech \
  --from-literal=DATABASE_URL="postgres://USER:PASS@HOST:5432/donation_db"

# volunteer-service não requer secret (usa IAM do node via LabRole)
```

5. Atualizar `AWS_SQS_URL` em `donation-service/configmap.yaml` com a URL real da fila SQS.

## Registrar Applications no ArgoCD

```bash
kubectl apply -f argocd-apps/
```

## Deploy

O pipeline CI/CD do repositório `postech-fase-5` atualiza automaticamente a tag da imagem neste repositório após build/push no ECR. O ArgoCD sincroniza as mudanças no cluster.
