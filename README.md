# Canarino Demo App

Applicazione minimale pensata per fare prove di rilascio canary su Kubernetes.

Questa repository contiene solo il codice dell'app e la build container:

- interfaccia statica servita con `nginx`
- `Dockerfile` pronto per build e publish su GHCR
- workflow GitHub Actions per costruire l'immagine container

La parte GitOps/Argo CD vive in una repository separata, pensata per essere la sorgente di verita dei manifest di deploy.

## Struttura

- `site/`: interfaccia statica
- `nginx/`: configurazione web server
- `.github/workflows/container.yml`: build e publish immagine su GHCR

## Build locale

```bash
docker build -t ghcr.io/lerma4/canarino-argocd-demo:local .
docker run --rm -p 8080:8080 ghcr.io/lerma4/canarino-argocd-demo:local
```

## Rilascio di una nuova versione

1. Aggiorna il contenuto statico in `site/`.
2. Fai push su `main` per generare una nuova immagine su GHCR.
3. Aggiorna il tag immagine nella repository GitOps di Argo CD.
4. Lascia che Argo CD sincronizzi il `Rollout` canary.

Per evitare `imagePullSecret` nel cluster, l'immagine GHCR usata dai manifest GitOps deve essere pubblica.
