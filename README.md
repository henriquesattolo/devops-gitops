# devops-gitops

🚀 GitOps com **ArgoCD** — deploy automático no Kubernetes a partir do Git, sem `kubectl apply` manual.

> Evolução natural do `devops-helm`. Lá o deploy é manual via `helm install`. Aqui o cluster **observa o Git** e aplica mudanças sozinho.

## Por que GitOps

- 🔄 **Single source of truth** = o repositório Git
- 🧰 **Rollback** trivial (basta `git revert`)
- 🔒 **Auditoria total** — toda mudança vira commit
- 🚫 **Sem credenciais de cluster** distribuídas em CI

## Stack

- **ArgoCD** — operador GitOps
- **kind** — cluster Kubernetes local (mesmo do `devops-helm`)
- **Helm** — para empacotar manifests
- **Kustomize** — para variações por ambiente

## Estrutura

```
devops-gitops/
├── apps/
│   ├── devops-app/
│   │   ├── base/                # manifests base
│   │   └── overlays/
│   │       ├── dev/
│   │       ├── staging/
│   │       └── prod/
├── argocd/
│   ├── install.sh
│   ├── projects/
│   └── applications/             # Application CRDs
│       ├── devops-app-dev.yaml
│       ├── devops-app-staging.yaml
│       └── devops-app-prod.yaml
└── README.md
```

## Demo (5 minutos)

```bash
# 1. Cria cluster kind
kind create cluster --name gitops

# 2. Instala ArgoCD
bash argocd/install.sh

# 3. Acessa a UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
# → https://localhost:8080
# user: admin, password: $(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)

# 4. Aplica os Application CRDs
kubectl apply -f argocd/applications/

# 5. Faz uma mudança e dá push — ArgoCD detecta e aplica em <1min
```

## Diferenciais

- ✅ **App-of-apps pattern** (uma Application gerencia outras)
- ✅ **Sync automático** com self-healing
- ✅ **Multi-ambiente** com Kustomize overlays
- ✅ **SSO opcional** (Dex)
- ✅ **Notificações** no Slack/Discord quando algo sincroniza

## Conexão com outros projetos

| Projeto base | Como evolui |
|---|---|
| `devops-helm` | Empacota o app aqui dentro de `apps/devops-app/base` |
| `devops-monitoring` | Vira mais uma Application gerenciada por GitOps |
| `devops-aws` | EKS substituindo o kind para um demo cloud |

## Autor

**Henrique Sattolo** — [github.com/henriquesattolo](https://github.com/henriquesattolo)
