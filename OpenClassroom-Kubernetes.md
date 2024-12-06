# Kubernetes - OpenClassroom

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
Remplacez _mon_image_ par le nom souhaité pour votre image Docker.

---

## Déployer une image avec Minikube
### 1. Créer un déploiement
Déployez votre image Docker dans Minikube :

```bash
minikube kubectl -- create deployment hello-minikube --image=mon_image
```
Le nom du déploiement est ici hello-minikube. Vous pouvez le modifier selon vos besoins.

### 2. Exposer un port pour le déploiement
   Exposez le déploiement pour le rendre accessible sur un port donné :

```bash
minikube kubectl -- expose deployment hello-minikube --type=LoadBalancer --port=3000
```
Cette commande expose le service sur le port 3000.

## Activer l'addon de registry pour Minikube
Pour activer la registry locale de Minikube, utilisez la commande suivante :

```bash
minikube addons enable registry
```