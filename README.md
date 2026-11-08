# Travelo sur Kubernetes (Minikube)

Application 3-tiers : React, Spring Boot, MySQL. Namespace : `travelo`.

## Prérequis
- Docker Desktop, Minikube, kubectl
- Addons : `minikube addons enable ingress` et `minikube addons enable metrics-server`
- Images chargées dans Minikube :
  `minikube image load younessessabri/travelo-backend:v1`
  `minikube image load younessessabri/travelo-frontend:v1`
  `minikube image load mysql:8.0`
- Fichier hosts (`C:\Windows\System32\drivers\etc\hosts`) :
  ```
  127.0.0.1 frontend.exam.local
  127.0.0.1 backend.exam.local
  ```

## Déploiement
```
kubectl create namespace travelo
kubectl apply -f secret.yaml -f configmap.yaml -f mysql.yaml
kubectl apply -f backend.yaml -f frontend.yaml
kubectl apply -f resourcequota.yaml -f limitrange.yaml -f rbac.yaml -f pdb.yaml
kubectl apply -f ingress.yaml
kubectl get all -n travelo
```

## Données de test
```
kubectl exec -i mysql-0 -n travelo -- mysql -uroot -p<mot-de-passe> travelo_db < flights.sql
```

## Accès
```
minikube tunnel
```
Puis ouvrir http://frontend.exam.local (le frontend appelle l'API sur http://backend.exam.local).

## Contenu
- `secret.yaml`, `configmap.yaml` : mot de passe MySQL et configuration
- `mysql.yaml` : StatefulSet + volume persistant + Service
- `backend.yaml`, `frontend.yaml` : Deployments (2 réplicas) + Services, avec probes
- `resourcequota.yaml`, `limitrange.yaml` : quotas CPU et mémoire
- `rbac.yaml` : ServiceAccount, Role, RoleBinding (lecture seule)
- `pdb.yaml` : PodDisruptionBudget
- `ingress.yaml` : accès par nom de domaine
- `flights.sql` : vols de test