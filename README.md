# Dockerisation des projets avec dunglas/symfony-docker sous Windows

Ce projet sert de base réutilisable pour dockeriser des projets Symfony sous Windows. Il s'appuie sur le template [dunglas/symfony-docker](https://github.com/dunglas/symfony-docker).

Pour plus d'informations, voir la section [documentation](https://github.com/dunglas/symfony-docker/tree/main/docs) du template.

---

## 📑 Table des matières

- [1. Prérequis](#1-prérequis)
- [2. Préparation du projet](#2-préparation-du-projet)
- [3. Personnalisation (Facultatif)](#3-personnalisation-facultatif)
- [4. Construction de l'image et lancement du projet](#4-construction-de-limage-et-lancement-du-projet)
  - [1. Construction de l'image Docker](#1-construction-de-limage-docker)
  - [2. Lancement du projet](#2-lancement-du-projet)
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

## 3. Personnalisation (Facultatif)

Il est possible de personnaliser certains éléments via des variables d'environnement :

```bash
set "IMAGES_PREFIX=app-nom-" & set "SERVER_NAME=app-nom.localhost" & set "POSTGRES_DB=app-nom-db" & set "POSTGRES_USER=app-nom-user" & set "POSTGRES_PASSWORD=app-nom-pswd"
```

| Variables d'environnement | Description |
|---------------------------|-------------|
| `IMAGES_PREFIX` | Nom du service PHP |
| `SERVER_NAME` | Nom / adresse du serveur |
| `POSTGRES_DB` | Nom de la base de données |
| `POSTGRES_USER` | Identifiant de la base de données |
| `POSTGRES_PASSWORD` | Mot de passe de la base de données |

> [!CAUTION]
> Il est important de reporter les variables et leurs valeurs dans le fichier `.env`, notamment pour `SERVER_NAME` et la configuration de la base de données.

---

## 4. Construction de l'image et lancement du projet

### 1. Construction de l'image Docker

```bash
docker compose build --no-cache
```

### 2. Lancement du projet

```bash
docker compose up --pull always -d --wait
```

---

## 5. Ajustements post-premier lancement

Selon les indications du template [dunglas/symfony-docker](https://github.com/dunglas/symfony-docker), après le premier lancement, il peut être utile de modifier certains fichiers :

- Dans `frankenphp/docker-entrypoint.sh` :
  **Commenter** le bloc de la ligne 7 à la ligne 23.

- Dans `compose.yaml` :
  **Commenter** les lignes 16 et 17.

- Dans `compose.override.yaml` :
  **Décommenter** la ligne 13.

---

## 6. Construction d'un projet web complet

Pour transformer le projet en une application Symfony complète :

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

   > [!NOTE]
   > Si le projet a été [personnalisé](#3-personnalisation-facultatif) via des variables d'environnement, il est prudent de les **vider** :
   >
   > ```bash
   > set "IMAGES_PREFIX=" & set "SERVER_NAME=" & set "POSTGRES_DB=" & set "POSTGRES_USER=" & set "POSTGRES_PASSWORD="
   > ```  

### 4. Arrêt et nettoyage des conteneurs  

   Pour stopper le projet et supprimer les conteneurs orphelins, utiliser :

   ```bash
   docker compose down --remove-orphans
   ```

---

## 7. Mise à jour du squelette

Pour mettre à jour le squelette de projet avec les dernières modifications du template, lancer la commande suivante :

```bash
curl -sSL https://raw.githubusercontent.com/coopTilleuls/template-sync/main/template-sync.sh | sh -s -- https://github.com/dunglas/symfony-docker
```

> [!TIP]
> VS Code permet d'exécuter cette commande dans un terminal WSL.
