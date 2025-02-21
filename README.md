# Dockerisation des projets avec dunglas/symfony-docker sous Windows

Ce projet sert de base réutilisable pour dockeriser des projets Symfony sous Windows. Il s'appuie sur le template [dunglas/symfony-docker](https://github.com/dunglas/symfony-docker). 

---

## 📑 Table des matières

- [1. Prérequis](#1-prérequis)
- [2. Préparation du projet](#2-préparation-du-projet)
- [3. Construction de l'image Docker](#3-construction-de-limage-docker)
  - [1. Construction standard](#1-construction-standard)
  - [2. Construction personnalisée du nom de l'image](#2-construction-personnalisée-du-nom-de-limage)
- [4. Lancement du projet](#4-lancement-du-projet)
  - [1. Lancement standard](#1-lancement-standard)
  - [2. Avec personnalisation du nom du serveur](#2-avec-personnalisation-du-nom-du-serveur)
- [5. Ajustements post-premier lancement](#5-ajustements-post-premier-lancement)
- [6. Construction d'un projet web complet](#6-construction-dun-projet-web-complet)
  - [1. Installation de Symfony](#1-installation-de-symfony)
  - [2. Reconstruction de l'image Docker](#2-reconstruction-de-limage-docker)
  - [3. Relance du projet](#3-relance-du-projet)
  - [4. Arrêt et nettoyage des conteneurs](#4-arrêt-et-nettoyage-des-conteneurs)
- [7. Mise à jour du squelette](#7-mise-à-jour-du-squelette)

---

## 1. Prérequis

Certains éléments sont nécessaires à l'utilisation du template :

- [PHP](https://www.php.net/downloads.php#:~:text=Binaries%20are%20available%20for%20Microsoft%20Windows)
- [Composer](https://getcomposer.org/download/)
- [WSL](https://learn.microsoft.com/fr-fr/windows/wsl/install)
- [Docker](https://www.docker.com/products/docker-desktop/#:~:text=Download%20Docker%20Desktop)

---

## 2. Préparation du projet

1. Copier le dossier `docker-symfony-base` et le renommer à convenance selon le projet.
2. Supprimer les éléments propres au template : 
   - `/.git` : pour démarrer un nouveau dépôt Git si besoin
   - `/.github`
   - `/docs`
   - `symfony-docker-README.md`

3. Ouvrir un terminal et se placer dans le dossier du projet. Ou ouvrir le projet directement dans un IDE.

---

## 3. Construction de l'image Docker

Deux façons de construire l'image sont disponibles : 
1. **Construction standard** : garde le nom du template comme nom d'image.

2. **Construction personnalisée** : permet de définir un nom spécifique.

### 1. Construction standard

```bash
docker compose build --no-cache
```

### 2. Construction personnalisée du nom de l'image

Pour personnaliser le nom de l'image (préfixe), utiliser la commande :

```bash
set IMAGES_PREFIX=nom_projet& docker compose build --no-cache
```

---

## 4. Lancement du projet

Comme pour la section précédente, deux options sont disponibles.

> [!WARNING]
> Si le nom de l'image a été personnalisé, utiliser l'option **2**.

### 1. Lancement standard

```bash
docker compose up --pull always -d --wait
```

### 2. Avec personnalisation du nom du serveur

```bash
set SERVER_NAME="nom_projet.localhost" && docker compose up --pull always -d --wait
```

> [!WARNING]
> Si le nom a été personnalisé, il est nécessaire de **réinitialiser** les variables d'environnement avant de lancer de nouvelles commandes.  
> Pour ce faire, exécuter :  
> ```bash
> set IMAGES_PREFIX=&set SERVER_NAME=
> ```  
> Puis ajouter ces variables dans le fichier `.env` du projet pour les conserver :
> ```dotenv
> IMAGES_PREFIX=nom_projet
> SERVER_NAME="nom_projet.localhost"
> ```

---

## 5. Ajustements post-premier lancement

Selon les indications du template [dunglas/symfony-docker](https://github.com/dunglas/symfony-docker), après le premier lancement, il peut être utile de modifier certains fichiers :

- Dans `frankenphp/docker-entrypoint.sh` :  
  **Commenter** le bloc de la ligne 7 à la ligne 23.

- Dans `compose.yaml` :  
  **Commenter** les lignes 16 et 17.

- Dans `compose.override.yaml` :  
  **Décommenter** la ligne 13.

Ces modifications permettent d’adapter le comportement des conteneurs en fonction des besoins.

---

## 6. Construction d'un projet web complet

Pour transformer le projet en une application web complète, procéder comme suit :

### 1. Installation de Symfony  
   Une fois le projet lancé, exécuter :
   ```bash
   docker compose exec php composer require webapp
   ```
   > [!NOTE]
   > Comme la commande précédente, l'exécution des commandes PHP doit être préfixée par `docker compose exec`.

### 2. Reconstruction de l'image Docker  
   Afin d’intégrer les changements, reconstruire l'image :
   ```bash
   docker compose build --no-cache --pull
   ```

### 3. Relance du projet  
   Il est possible de relancer le projet de deux manières :

   1. En récupérant les images depuis le registre Docker :
      ```bash
      docker compose up --pull always -d --wait
      ```  
   2. En utilisant les images disponibles en local :
      ```bash
      docker compose up -d --wait
      ```

   Le projet est maintenant accessible via `https://localhost`, ou `https://nom_projet.localhost` si son nom a été personnalisé.

### 4. Arrêt et nettoyage des conteneurs  
   Pour stopper le projet et supprimer les conteneurs orphelins, utiliser :
   ```bash
   docker compose down --remove-orphans
   ```

---

## 7. Mise à jour du squelette

Pour mettre à jour le squelette de projet avec les dernières modifications du template, lancer la commande suivante :

```bash
curl -sSL https://raw.githubusercontent.com/coopTilleuls/template-sync/main/template-sync.sh | sh -s -- https://github.com/dunglas/symfony-docker
```

> [!TIP]
> VS Code permet d'exécuter cette commande dans un terminal WSL.