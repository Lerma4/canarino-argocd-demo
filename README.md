# Canarino Argo CD Demo

Progetto minimale pensato per fare prove di rilascio canary su Kubernetes con Argo CD.

Include:

- interfaccia statica servita con `nginx`
- `Dockerfile` pronto per build e publish su GHCR
- manifest Kubernetes con `Rollout` di Argo Rollouts
- manifest `Application` per Argo CD
- workflow GitHub Actions per costruire l'immagine container

## Struttura

- `site/`: interfaccia statica
- `nginx/`: configurazione web server
- `k8s/base/`: namespace, service e rollout
- `k8s/overlays/prod/`: overlay Kustomize per Argo CD
- `argocd/application.yaml`: esempio di applicazione Argo CD

## Requisiti cluster

- Argo CD installato
- Argo Rollouts installato
- accesso del cluster al repository GitHub privato
- `imagePullSecret` per leggere l'immagine privata su GHCR

## Build locale

```bash
docker build -t ghcr.io/lerma4/canarino-argocd-demo:local .
docker run --rm -p 8080:8080 ghcr.io/lerma4/canarino-argocd-demo:local
```

## Deploy con Argo CD

1. Registra il repo privato in Argo CD.
2. Crea nel namespace `canarino` un secret Docker per GHCR.
3. Applica `argocd/application.yaml` nel namespace di Argo CD.

```bash
kubectl apply -n argocd -f argocd/application.yaml
```

## Rilascio di una nuova versione

1. Aggiorna il contenuto statico in `site/`.
2. Fai merge su `main` per generare una nuova immagine su GHCR.
3. Aggiorna il tag immagine in `k8s/overlays/prod/kustomization.yaml`.
4. Lascia che Argo CD sincronizzi il `Rollout` canary.

Se vuoi automatizzare il bump immagine puoi aggiungere Argo CD Image Updater in un secondo momento.
