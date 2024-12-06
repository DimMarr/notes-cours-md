# Kubernetes - OpenClassroom

[[_TOC_]]

## Pré-requis : Télécharger Minikube
Rendez-vous sur la [documentation officielle de Minikube](https://minikube.sigs.k8s.io/docs/start/) pour télécharger et installer Minikube sur votre machine.

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
minikube kubectl -- create deployment hello-minikube --image=mon_image
```
Le nom du déploiement est ici _hello-minikube_. Vous pouvez le modifier selon vos besoins.

### 2. Exposer un port pour le déploiement
Exposez le déploiement pour le rendre accessible sur un port donné :
```bash
minikube kubectl -- expose deployment hello-minikube --type=LoadBalancer --port=3000
```
Cette commande expose le service sur le port 3000.

## Registry pour Minikube
Pour activer la registry locale de Minikube, utilisez la commande suivante :
```bash
minikube addons enable registry
```

### 1. Exposer la registry publiquement
Par défaut, la registry écoute sur le port 80 de la machine Minikube. Pour l'exposer sur le port 5000 (par convention) :
```bash
minikube kubectl -- port-forward --namespace kube-system service/registry 5000:80
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