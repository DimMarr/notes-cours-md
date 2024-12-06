# Kubernetes - OpenClassroom

* [Pré-requis](#pré-requis--télécharger-minikube)
* [Commandes de base](#commandes-de-base)
  * [Démarrer Minikube](#1-démarrer-minikube)
  * [Vérifier le statut de Minikube](#2-vérifier-le-statut-de-minikube)
* [Construire une image Docker pour Minikube](#construire-une-image-docker-pour-minikube)
  * [Configurer Docker](#1-configurer-docker-pour-lenvironnement-de-minikube)
  * [Construire une image](#2-construire-une-image)
* [Déployer une image avec Minikube](#déployer-une-image-avec-minikube)
  * [Créer un déploiement](#1-créer-un-déploiement)
  * [Exposer un port](#2-exposer-un-port-pour-le-déploiement)
* [Registry pour Minikube](#registry-pour-minikube)
    * [Exposer la registry](#1-exposer-la-registry)
    * [Vérifier que la registry fonctionne](#2-vérifier-que-la-registry-fonctionne)
    * [Taguer et uploader une image dans la registry](#3-taguer-et-uploader-une-image-dans-la-registry)
* [Les Pod Kubernetes](#les-pod-kubernetes)
  * [Init Container](#init-container)
  * [Side Car Container](#side-car-container)
  * [Cycle de vie d'un Pod](#cycle-de-vie-dun-pod)
  * [Déployer un Pod](#déployer-un-pod)
  * [Vérifier l'état des Pods](#vérifier-létat-des-pods)
  * [Supprimer un Pod](#supprimer-un-pod)
* [Fichier Kubernetes de Deployment YAML](#fichier-kubernetes-de-deployment-yaml)
  * [Appliquer un fichier de déploiement YAML](#appliquer-un-fichier-de-déploiement-yaml)
  * [Vérifier le déploiement](#vérifier-le-déploiement)
* [Analysez le déploiement](#analysez-le-déploiement)
## Pré-requis
Rendez-vous sur la [documentation officielle de Minikube](https://minikube.sigs.k8s.io/docs/start/) pour télécharger et installer Minikube sur votre machine.

Pour vérifier que Minikube est bien installé, exécutez la commande suivante :
```bash
minikube version
```
On peut créer un alias pour la commande `minikube kubectl` pour simplifier les commandes :
```bash
alias kubectl='minikube kubectl --'
```

Dans la suite de ce document, on utilisera la commande `kubectl` pour exécuter des commandes sur Minikube.

---

## Commandes de base

### 1. Démarrer Minikube
Lancez Minikube avec la commande suivante :
```bash
minikube start
```

### 2. Vérifier le statut de Minikube
Pour vous assurer que Minikube est bien démarré :
```bash
minikube status
```
---

## Construire une image Docker pour Minikube
### 1. Configurer Docker pour l'environnement de Minikube
Cette commande permet de configurer Docker pour qu'il utilise Minikube :
```bash
eval $(minikube docker-env)
```

### 2. Construire une image
Construisez votre image Docker localement avec :

```bash
docker build . -t mon_image
```
Remplacez _mon_image_ par le nom souhaité pour l'image Docker.

---

## Déployer une image avec Minikube
### 1. Créer un déploiement
Déployez votre image Docker dans Minikube :
```bash
kubectl create deployment hello-minikube --image=mon_image
```
Le nom du déploiement est ici _hello-minikube_. Vous pouvez le modifier selon vos besoins.

### 2. Exposer un port pour le déploiement
Exposez le déploiement pour le rendre accessible sur un port donné :
```bash
kubectl expose deployment hello-minikube --type=LoadBalancer --port=3000
```
Cette commande expose le service sur le port 3000.

---

## Registry pour Minikube
Pour activer la registry locale de Minikube, utilisez la commande suivante :
```bash
minikube addons enable registry
```

### 1. Exposer la registry
Par défaut, la registry écoute sur le port 80 de la machine Minikube. Pour l'exposer sur le port 5000 (par convention) :
```bash
kubectl port-forward --namespace kube-system service/registry 5000:80
```
_Gardez cette fenêtre ouverte pour maintenir la connexion._

### 2. Vérifier que la registry fonctionne
Testez la connexion avec :

```bash
curl http://localhost:5000/v2/_catalog
```
Si la commande fonctionne, vous obtiendrez une réponse similaire à :
```json
{"repositories":[""]}
```

### 3. Taguer et uploader une image dans la registry
On commence par taguer l'image Docker avec l'adresse de la registry :
```bash
docker tag mon_image localhost:5000/mon_image
```

Ensuite, on pousse l'image taguée dans la registry :
```bash
docker push localhost:5000/mon_image
```

On vérifie que l'image est bien présente dans la registry :
```bash
curl http://localhost:5000/v2/_catalog
```
La réponse devrait contenir le nom de l'image.

--- 

## Les Pod Kubernetes
Un Pod est l'unité de base de Kubernetes. 
Il s'agit d'un ensemble d'un ou plusieurs conteneurs qui partagent un même espace de stockage et une même adresse IP. 

### Init Container
Un init container est un conteneur qui s'exécute avant les autres conteneurs d'un Pod.
Il permet de réaliser des tâches de configuration ou de préparation avant le démarrage des conteneurs principaux.

### Side Car Container
Un side car container est un conteneur qui s'exécute en parallèle des autres conteneurs d'un Pod.
Les sidecar containers exécutent généralement des fonctions de support comme par exemple récupérer les logs de l’application et les envoyer à un serveur central, ajouter une couche d’authentification ou avoir une gestion fine des permissions applicatives.

### Cycle de vie d'un Pod
Un Pod peut être dans l'un des états suivants :
- Pending : le Pod est presque prêt à être déployé
- Running : le Pod est en cours d'exécution
- Succeeded : le Pod a terminé son exécution avec succès
- Failed : le Pod a échoué
- Unknown : l'état du Pod est inconnu
- Terminating : le Pod est en cours de suppression
- Terminated : le Pod a été supprimé

### Déployer un Pod
Pour déployer un Pod, on utilise la commande `kubectl run` :
```bash
kubectl run nginx --image=nginx --port=80
```

### Vérifier l'état des Pods
Pour vérifier l'état des Pods du namespace par défaut, on utilise la commande `kubectl get pods` :
```bash
kubectl get pods
```
On peut ajouter le flag `-n` pour spécifier le namespace :
```bash
kubectl get pods -n mon_namespace
```
Ou ajouter le flag `-a` pour afficher tous les Pods du cluster :
```bash
kubectl get pods -a
```
La liste de tous les flags est disponible ici : [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get)
### Supprimer un Pod
Pour supprimer un Pod, on utilise la commande `kubectl delete` :
```bash
kubectl delete pod/mon_pod
```

---

## Fichier Kubernetes de Deployment YAML
Un fichier de déploiement YAML permet de décrire un déploiement Kubernetes.
Voici un exemple de fichier de déploiement pour un Pod Nginx :
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
    replicas: 3
    selector:
        matchLabels:
        app: nginx
    template:
        metadata:
        labels:
            app: nginx
        spec:
        containers:
        - name: nginx
            image: nginx:1.14.2
            ports:
            - containerPort: 8080
```

On définit un déploiement nommé `nginx-deployment` avec 3 réplicas du Pod Nginx.
Le Pod Nginx utilise l'image `nginx:1.14.2` et expose le port 8080.
On utilise la version `apps/v1` de l'API Kubernetes pour définir le déploiement.

### Appliquer un fichier de déploiement YAML
Pour appliquer un fichier de déploiement YAML, on utilise la commande `kubectl apply` :
```bash
kubectl apply -f mon_fichier.yaml
```

### Vérifier le déploiement
On peut vérifier l'état du déploiement avec la commande `kubectl get deploy` :
```bash
kubectl get deploy
```

---

## Analysez le déploiement
Pour diagnostiquer une erreur de déploiement, on peut consulter les logs des Pods :
```bash
kubectl logs mon_pod
```
Vous pouvez spécifier le nom du pod ou utiliser un sélecteur pour cibler plusieurs pods.
La commande suivante permet de récupérer les logs de tous les pods d'un déploiement :
```bash
kubectl logs -l app=mon_deployment
```
Dans le cas où le Pod a plusieurs conteneurs, vous pouvez spécifier le conteneur dont vous
souhaitez récupérer les logs :
```bash
kubectl logs mon_pod -c mon_conteneur
```


