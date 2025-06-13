# MiniShop – Architecture microservices e-commerce

## Auteurs
- Antony PINEL
- Anthony COCCO
- Jonathan JAREMCZUK

## Présentation
MiniShop est une architecture microservices simple pour un site e-commerce, composée de trois services principaux :
- **Product Service** : gestion des produits (catalogue, stock, etc.)
- **Cart Service** : gestion des paniers utilisateurs (ajout, suppression, calcul du total, etc.)
- **Order Service** : gestion des commandes (création, mise à jour du statut, etc.)

Chaque service dispose de sa propre base de données PostgreSQL et est exposé via une API REST (avec documentation Swagger). L'ensemble est prêt à être déployé sur Kubernetes.

## Structure du projet
```
product-service/ (API REST, gestion des produits)
cart-service/ (API REST, gestion des paniers)
order-service/ (API REST, gestion des commandes)
k8s/ (fichiers de déploiement Kubernetes)
```

## Prérequis
- Docker (pour construire et exécuter les images)
- Kubernetes (par exemple, minikube, k3d ou Docker Desktop avec Kubernetes activé)
- Make (pour exécuter les commandes de build et déploiement)
- Node.js (pour exécuter les tests ou démarrer les services en mode dev)

## Déploiement rapide (via Makefile)

### 1. Construire les images Docker
Exécutez la commande suivante à la racine du projet :
```sh
make build
```
Cela construit les images Docker pour les trois services (product, cart, order) avec le tag `minishop/<service>:latest`.

### 2. Déployer sur Kubernetes
Exécutez :
```sh
make k8s-deploy
```
Cette commande applique les fichiers de configuration Kubernetes (namespace, ConfigMaps, PersistentVolumes, PersistentVolumeClaims, déploiements et services) dans le namespace `minishop`.

### 3. Accéder aux services
- **Exposition des services** : Par défaut, les services sont exposés en interne (ClusterIP). Pour les tester depuis l'extérieur, utilisez par exemple :
  ```sh
  kubectl port-forward -n minishop svc/product-service 3000:3000
  kubectl port-forward -n minishop svc/cart-service 3001:3000
  kubectl port-forward -n minishop svc/order-service 3002:3000
  ```
- **Documentation Swagger** : Chaque service expose sa documentation OpenAPI sur `/api`. Par exemple, après avoir lancé le port-forward, ouvrez dans votre navigateur :
  - Product Service : [http://localhost:3000/api](http://localhost:3000/api)
  - Cart Service : [http://localhost:3001/api](http://localhost:3001/api)
  - Order Service : [http://localhost:3002/api](http://localhost:3002/api)

### 4. Nettoyer le cluster
Pour supprimer toutes les ressources déployées (déploiements, services, ConfigMaps, PVC, PV, namespace), exécutez :
```sh
make k8s-clean
```

## Ressources supplémentaires
- **Dockerfile** : Chaque service contient un Dockerfile (à la racine du dossier) pour construire l'image.
- **Makefile** : Le fichier Makefile à la racine du projet contient les commandes pour build, push (optionnel) et déployer sur Kubernetes.
- **Kubernetes** : Les fichiers de déploiement (namespace, ConfigMaps, PV, PVC, déploiements, services) se trouvent dans le dossier `k8s/`.

---