# 🐳 Conteneurs - Docker et Podman

---

## 🎯 Conteneurs : Concepts

**Conteneur** = Environnement applicatif **isolé** partageant le noyau hôte

**Différence VM vs Conteneur** :

| Aspect | Machine Virtuelle | Conteneur |
|--------|-------------------|-----------|
| **OS complet** | ✅ Oui (noyau + userland) | ❌ Non (partage noyau hôte) |
| **Taille** | 🔴 Lourd (Go) | 🟢 Léger (Mo) |
| **Démarrage** | 🔴 Lent (minutes) | 🟢 Rapide (secondes) |
| **Isolation** | 🟢 Forte (hyperviseur) | 🟡 Moyenne (namespaces/cgroups) |
| **Overhead** | 🔴 Élevé | 🟢 Faible |
| **Usage** | Infrastructure complète | Applications microservices |

**Analogie** 🏢 :
- **VM** = Immeuble entier (tout inclus, lourd)
- **Conteneur** = Appartement (partage fondations, léger)

---

### Architecture Conteneur

```
┌─────────────────────────────────────┐
│  Conteneur 1   Conteneur 2   Cont 3 │ ← Apps isolées
│  (nginx)       (mysql)       (redis) │
├─────────────────────────────────────┤
│  Runtime Conteneur (Docker/Podman)  │ ← Gestion conteneurs
├─────────────────────────────────────┤
│  Noyau Linux (cgroups, namespaces)  │ ← Isolation ressources
├─────────────────────────────────────┤
│  Matériel Physique                  │
└─────────────────────────────────────┘
```

**Technologies Linux** :
- **namespaces** : Isolation (PID, réseau, montages...)
- **cgroups** : Limitation ressources (CPU, RAM, I/O)
- **Union filesystems** : Couches images (OverlayFS, AUFS)

---

## 🔴 Podman : Alternative Red Hat à Docker

### Historique CentOS 8 / RHEL 8

**Problème** : Docker **non inclus** dans dépôts officiels RHEL/CentOS 8

**Solution Red Hat** : **Podman** (Pod Manager)

**Avant (CentOS 7)** :
```bash
yum install docker
systemctl start docker
docker run nginx
```

**Après (CentOS 8+)** :
```bash
yum install podman
# Pas de daemon à démarrer !
podman run nginx

# OU alias automatique
docker run nginx  # → Redirige vers podman
```

---

### Différences Docker vs Podman

| Aspect | Docker | Podman |
|--------|--------|--------|
| **Daemon** | ✅ Oui (`dockerd`) | ❌ Non (daemonless) |
| **Root requis** | ⚠️ Oui (par défaut) | 🟢 Non (rootless possible) |
| **Compatibilité** | Docker CLI | 🟢 Rétrocompatible Docker CLI |
| **Pods Kubernetes** | ❌ Non | ✅ Oui (gestion pods) |
| **Éditeur** | Docker Inc. | Red Hat |
| **Architecture** | Client-serveur | Fork direct |

**Analogie** 🚗 :
- **Docker** = Voiture avec chauffeur (daemon)
- **Podman** = Voiture autonome (pas de daemon)

---

### Alias docker → podman

**CentOS 8 / RHEL 8** :

```bash
# Installation podman
sudo dnf install podman

# Vérifier alias (automatique sur RHEL 8)
which docker
# /usr/bin/docker

file /usr/bin/docker
# /usr/bin/docker: symbolic link to /usr/bin/podman

# OU créer alias manuellement
alias docker=podman
echo "alias docker=podman" >> ~/.bashrc
```

**Effet** : Toutes commandes `docker` → `podman`

**💡 Avantage** : Scripts Docker existants **fonctionnent** avec Podman

---

## ⚙️ Configuration Podman : registries.conf

### Fichier /etc/containers/registries.conf

**Rôle** : Définir **registres d'images** (équivalent Docker Hub)

**Emplacement** : `/etc/containers/registries.conf`

---

### Structure Fichier

```toml
# Registres pour recherche non qualifiée
unqualified-search-registries = ["registry.fedoraproject.org", "registry.access.redhat.com", "docker.io", "quay.io"]

# Registres bloqués
[[registry]]
location = "blocked-registry.com"
blocked = true

# Registre avec miroir
[[registry]]
location = "docker.io"
[[registry.mirror]]
location = "mirror.example.com"
```

---

### Directive unqualified-search-registries

**Problème** : Podman utilise **plusieurs registres** par défaut

**Effet** :
```bash
podman pull nginx
# Recherche dans :
# 1. registry.fedoraproject.org/nginx
# 2. registry.access.redhat.com/nginx
# 3. docker.io/nginx  ← Docker Hub
# 4. quay.io/nginx
```

**Solution** : Forcer **uniquement Docker Hub**

---

### Configuration Docker Hub Uniquement

**Éditer** :
```bash
sudo nano /etc/containers/registries.conf
```

**Modifier** :
```toml
# Ne chercher que sur Docker Hub
unqualified-search-registries = ["docker.io"]
```

**Effet** :
```bash
podman pull nginx
# Recherche UNIQUEMENT sur docker.io/library/nginx
```

**💡 Équivalent** : Comportement identique à Docker

---

### Vérifier Configuration

```bash
# Afficher config active
podman info | grep -A 5 registries

# Tester pull
podman pull nginx
# Trying to pull docker.io/library/nginx:latest...
```

---

## 📄 Fichier /etc/containers/nodocker

### Rôle

**Problème** : Commande `docker` via Podman affiche **avertissements**

**Exemple** :
```bash
docker run nginx
# Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
```

**Solution** : Créer fichier `/etc/containers/nodocker`

---

### Création Fichier

```bash
sudo touch /etc/containers/nodocker
```

**Effet** : Supprimer message avertissement

**Avant** :
```bash
docker ps
# Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
# CONTAINER ID  IMAGE  ...
```

**Après** :
```bash
docker ps
# CONTAINER ID  IMAGE  ...
```

**💡 Optionnel** : Purement cosmétique (pas d'impact fonctionnel)

---

## 🆘 Aide Commandes

### Aide Générale

```bash
docker --help
```

**Résultat** (extrait) :
```
Usage:  docker [OPTIONS] COMMAND

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  exec        Run a command in a running container
  images      List images
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker COMMAND --help' for more information on a command.
```

---

### Aide Sous-Commande

```bash
docker COMMANDE --help
```

**Exemple** :
```bash
docker rm --help
```

**Résultat** :
```
Usage:  docker rm [OPTIONS] CONTAINER [CONTAINER...]

Remove one or more containers

Options:
  -f, --force     Force the removal of a running container (uses SIGKILL)
  -l, --link      Remove the specified link
  -v, --volumes   Remove anonymous volumes associated with the container
```

**💡 Pratique** : Syntaxe exacte + options disponibles

---

## 🔍 docker search : Chercher Images

### Syntaxe

```bash
docker search nom-image
```

**Rôle** : Rechercher images dans registres configurés

---

### Exemple

```bash
docker search nginx
```

**Résultat** :
```
NAME                               DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                              Official build of Nginx.                        19000     [OK]       
jwilder/nginx-proxy                Automated Nginx reverse proxy for docker con…   2200                 [OK]
richarvey/nginx-php-fpm            Container running Nginx + PHP-FPM capable of…   900                  [OK]
bitnami/nginx                      Bitnami nginx Docker Image                      180                  [OK]
linuxserver/nginx                  An Nginx container, brought to you by LinuxS…   180                  
```

**Colonnes** :
- **NAME** : Nom image (format `utilisateur/nom` ou `nom` si officielle)
- **DESCRIPTION** : Description courte
- **STARS** : Popularité
- **OFFICIAL** : Image officielle (maintenue éditeur)
- **AUTOMATED** : Build automatique depuis GitHub

---

### Options Utiles

```bash
# Filtrer images officielles
docker search --filter is-official=true nginx

# Limiter résultats
docker search --limit 5 nginx

# Minimum étoiles
docker search --filter stars=1000 nginx
```

**💡 Image officielle** : Préférer `nginx` à `utilisateur/nginx`

---

## ⬇️ docker pull : Télécharger Image

### Syntaxe

```bash
docker pull nom-image
```

**Rôle** : Télécharger image depuis registre

**Par défaut** : Tag `latest` (dernière version)

---

### Exemple Pull Simple

```bash
docker pull nginx
```

**Résultat** :
```
Using default tag: latest
latest: Pulling from library/nginx
a2abf6c4d29d: Pull complete 
a9edb18cadd1: Pull complete 
589b7251471a: Pull complete 
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

**Processus** :
1. ✅ Télécharge couches image (layers)
2. ✅ Vérifie intégrité (SHA256)
3. ✅ Stocke localement

---

### docker pull avec Tag Spécifique

```bash
docker pull nom-image:tag
```

**Tag** : Version spécifique (ex: `1.25`, `alpine`, `stable`)

---

**Exemples** :

```bash
# Version spécifique
docker pull nginx:1.25.3

# Version alpine (légère)
docker pull nginx:alpine

# Version stable
docker pull nginx:stable

# Version anciennes
docker pull ubuntu:20.04
docker pull mysql:5.7
```

**Vérifier tags disponibles** : https://hub.docker.com → Image → Tags

---

### Nom Complet vs Court

| Format | Exemple | Registre |
|--------|---------|----------|
| **Court** | `nginx` | docker.io/library/nginx (par défaut) |
| **Utilisateur** | `jwilder/nginx-proxy` | docker.io/jwilder/nginx-proxy |
| **Complet** | `docker.io/library/nginx:1.25` | Explicite |
| **Autre registre** | `quay.io/nginx/nginx` | Quay.io |

---

## 📋 docker images : Lister Images

### Syntaxe

```bash
docker images
```

**Alias** :
```bash
docker image list
docker image ls
```

---

### Exemple

```bash
docker images
```

**Résultat** :
```
REPOSITORY          TAG       IMAGE ID       CREATED        SIZE
nginx               latest    605c77e624dd   2 weeks ago    141MB
nginx               alpine    f9c14fe76d50   3 weeks ago    23.5MB
mysql               5.7       c20987f18b13   4 weeks ago    448MB
ubuntu              20.04     ba6acccedd29   5 weeks ago    72.8MB
redis               latest    7614ae9453d1   6 weeks ago    113MB
```

**Colonnes** :
- **REPOSITORY** : Nom image
- **TAG** : Version/variante
- **IMAGE ID** : Identifiant unique (12 premiers caractères SHA256)
- **CREATED** : Date création
- **SIZE** : Taille image

---

### Options Utiles

```bash
# Afficher toutes images (y compris intermédiaires)
docker images -a

# Filtrer par nom
docker images nginx

# Format personnalisé
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Afficher IDs uniquement
docker images -q
# 605c77e624dd
# f9c14fe76d50
# c20987f18b13
```

---

## 🗑️ docker rmi : Supprimer Image

### Syntaxe

```bash
docker rmi nom-image:tag
```

**Alias** :
```bash
docker image rm nom-image:tag
docker image remove nom-image:tag
```

**Rôle** : Supprimer image locale (libérer espace)

---

### Exemple

```bash
docker rmi nginx:alpine
```

**Résultat** :
```
Untagged: nginx:alpine
Untagged: nginx@sha256:...
Deleted: sha256:f9c14fe76d502861ba0939bc3189e642c02e257f06f4c0214b1f8ca329326cda
Deleted: sha256:...
```

---

### Référencer Images

**4 méthodes** :

1. **Nom + Tag** : `nginx:1.25`
2. **Nom seul** : `nginx` (supprime tag `latest` uniquement)
3. **Image ID** : `605c77e624dd`
4. **Repository complet** : `docker.io/library/nginx:alpine`

**Exemples** :

```bash
# Par nom + tag
docker rmi nginx:alpine

# Par nom (latest implicite)
docker rmi nginx

# Par Image ID
docker rmi 605c77e624dd

# Repository complet
docker rmi docker.io/library/nginx:1.25
```

---

### docker rmi --force : Forcer Suppression

**Problème** : Image utilisée par conteneur

```bash
docker rmi nginx:latest
# Error response from daemon: conflict: unable to remove repository reference "nginx:latest" 
# (must force) - container abc123 is using its referenced image 605c77e624dd
```

**Solution** : Forcer avec `--force`

```bash
docker rmi --force nginx:latest
```

**Effet** :
1. ✅ **Arrête** conteneurs utilisant l'image
2. ✅ **Supprime** conteneurs
3. ✅ **Supprime** image

**⚠️ DANGER** : Perte données conteneurs non sauvegardés

**💡 Mieux** : Arrêter/supprimer conteneurs manuellement d'abord

---

## 🚀 docker run : Démarrer Conteneur

### Syntaxe

```bash
docker run [OPTIONS] image [COMMANDE]
```

**Rôle** : Créer **et** démarrer conteneur depuis image

**Équivalent** :
```bash
docker create nom-image  # Créer
docker start container-id  # Démarrer
```

---

### Exemple Simple

```bash
docker run nginx
```

**Résultat** :
```
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
...
2024/02/01 10:30:45 [notice] 1#1: start worker processes
```

**⚠️ Bloquant** : Terminal attaché (Ctrl+C pour arrêter)

---

### docker run -d : Mode Détaché

```bash
docker run -d nginx
```

**Alias** :
```bash
docker run --detach nginx
```

**Résultat** :
```
a1b2c3d4e5f6789012345678901234567890abcdef1234567890abcdef123456
```

**Effet** :
- ✅ Conteneur démarre en **arrière-plan**
- ✅ Renvoie **Container ID**
- ✅ Terminal **libre** (pas bloqué)

**💡 Usage production** : Toujours `-d` (daemon)

---

### Vérifier Conteneur Running

```bash
docker ps
# CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
# a1b2c3d4e5f6   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   80/tcp    xenodochial_turing
```

---

## 📋 docker ps : Lister Conteneurs

### docker ps : Conteneurs Actifs

```bash
docker ps
```

**Alias** :
```bash
docker container list
docker container ls
```

**Résultat** :
```
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                NAMES
a1b2c3d4e5f6   nginx:latest   "/docker-entrypoint.…"   2 minutes ago    Up 2 minutes    80/tcp               web-server
b7c8d9e0f1a2   mysql:5.7      "docker-entrypoint.s…"   10 minutes ago   Up 10 minutes   3306/tcp, 33060/tcp  db
```

**Colonnes** :
- **CONTAINER ID** : ID court (12 caractères)
- **IMAGE** : Image source
- **COMMAND** : Commande exécutée
- **CREATED** : Date création
- **STATUS** : État (Up = running)
- **PORTS** : Ports exposés
- **NAMES** : Nom conteneur (auto-généré ou custom)

---

### docker ps --all : Tous Conteneurs

```bash
docker ps --all
```

**Alias** :
```bash
docker ps -a
docker container ls --all
```

**Résultat** :
```
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
a1b2c3d4e5f6   nginx     "/docker-entrypoint.…"   5 minutes ago    Up 5 minutes                80/tcp    web-server
c3d4e5f6a7b8   ubuntu    "/bin/bash"              10 minutes ago   Exited (0) 2 minutes ago              test-container
e5f6a7b8c9d0   redis     "docker-entrypoint.s…"   1 hour ago       Exited (137) 30 minutes ago           cache
```

**États** :
- `Up X minutes` → Running
- `Exited (0)` → Arrêté proprement
- `Exited (137)` → Tué (SIGKILL)
- `Created` → Créé mais jamais démarré

**💡 Usage** : Voir conteneurs arrêtés (pour redémarrer ou supprimer)

---

### Options Utiles

```bash
# Derniers conteneurs créés
docker ps -n 5

# Uniquement IDs
docker ps -q
# a1b2c3d4e5f6
# b7c8d9e0f1a2

# Format personnalisé
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"
```

---

## 🛑 docker stop : Arrêter Conteneur

### Syntaxe

```bash
docker stop container-id-ou-nom
```

**Rôle** : Arrêter conteneur **proprement** (SIGTERM)

---

### Exemple

```bash
docker stop web-server
```

**Ou par ID** :
```bash
docker stop a1b2c3d4e5f6
```

**Résultat** :
```
web-server
```

**Processus** :
1. ✅ Envoyer SIGTERM (signal arrêt propre)
2. ⏱️ Attendre 10 secondes
3. ⚡ Si toujours running → SIGKILL (brutal)

**Vérifier** :
```bash
docker ps -a
# CONTAINER ID   IMAGE   STATUS                     NAMES
# a1b2c3d4e5f6   nginx   Exited (0) 5 seconds ago   web-server
```

---

### Arrêter Plusieurs Conteneurs

```bash
# Par noms
docker stop web-server db cache

# Tous conteneurs actifs
docker stop $(docker ps -q)
```

---

## ▶️ docker start : Démarrer Conteneur

### Syntaxe

```bash
docker start container-id-ou-nom
```

**Rôle** : Démarrer conteneur **existant** arrêté

**⚠️ Différence** :
- `docker run` → Créer **nouveau** conteneur
- `docker start` → Redémarrer conteneur **existant**

---

### Exemple

```bash
docker start web-server
```

**Résultat** :
```
web-server
```

**Vérifier** :
```bash
docker ps
# CONTAINER ID   IMAGE   STATUS        NAMES
# a1b2c3d4e5f6   nginx   Up 2 seconds  web-server
```

---

### Démarrer + Attacher

```bash
# Démarrer et attacher terminal
docker start -a web-server

# Démarrer et attacher + interaction
docker start -ai web-server
```

---

## 🗑️ docker rm : Supprimer Conteneur

### Syntaxe

```bash
docker rm container-id-ou-nom
```

**Rôle** : Supprimer conteneur **arrêté**

**⚠️ Pré-requis** : Conteneur doit être **stopped**

---

### Exemple

```bash
# 1. Arrêter conteneur
docker stop web-server

# 2. Supprimer
docker rm web-server
```

**Résultat** :
```
web-server
```

**Vérifier** :
```bash
docker ps -a
# (web-server n'apparaît plus)
```

---

### docker rm -f : Forcer Suppression

```bash
docker rm -f web-server
```

**Effet** : Arrêter **puis** supprimer (en une commande)

---

### Supprimer Plusieurs Conteneurs

```bash
# Par noms
docker rm container1 container2 container3

# Tous conteneurs arrêtés
docker rm $(docker ps -a -q -f status=exited)

# Nettoyer tous conteneurs arrêtés
docker container prune
# WARNING! This will remove all stopped containers.
# Are you sure you want to continue? [y/N] y
```

---

## ⚙️ Options docker run Avancées

### Option -d : Détacher

```bash
docker run -d nginx
```

**Effet** : Conteneur en arrière-plan

**Déjà vu** ✅

---

### Option -p : Redirection Port

```bash
docker run -p port-hôte:port-conteneur image
```

**Rôle** : Mapper port hôte → port conteneur

---

**Exemples** :

```bash
# Nginx : Port 80 conteneur → 8080 hôte
docker run -d -p 8080:80 nginx
# Accès : http://localhost:8080

# MySQL : Port 3306 conteneur → 3306 hôte
docker run -d -p 3306:3306 mysql:5.7

# Plusieurs ports
docker run -d -p 80:80 -p 443:443 nginx

# Port aléatoire hôte
docker run -d -p 80 nginx  # Hôte choisit port automatiquement
```

**Vérifier** :
```bash
docker ps
# PORTS
# 0.0.0.0:8080->80/tcp
```

**Tester** :
```bash
curl http://localhost:8080
# <!DOCTYPE html>
# <html>
# <title>Welcome to nginx!</title>
```

---

### Option --name : Nom Personnalisé

```bash
docker run --name mon-nom image
```

**Rôle** : Affecter nom **lisible** au lieu d'auto-généré

---

**Sans --name** :
```bash
docker run -d nginx
docker ps
# NAMES
# xenodochial_turing  ← Nom aléatoire
```

**Avec --name** :
```bash
docker run -d --name web-prod nginx
docker ps
# NAMES
# web-prod  ← Nom choisi
```

**💡 Avantages** :
- ✅ Référencer facilement : `docker stop web-prod`
- ✅ Clarté (environnement, rôle...)
- ✅ Scripts lisibles

---

### Combiner Options

```bash
docker run -d -p 8080:80 --name web-server nginx
```

**Effet** :
- `-d` : Arrière-plan
- `-p 8080:80` : Port 8080 hôte → 80 conteneur
- `--name web-server` : Nom personnalisé

**Résultat** :
```bash
docker ps
# CONTAINER ID   IMAGE   PORTS                 NAMES
# a1b2c3d4e5f6   nginx   0.0.0.0:8080->80/tcp  web-server
```

**Accès** :
```bash
curl http://localhost:8080
# Page nginx
```

---

## 🔐 Well-Known Ports et Root

### Ports Privilégiés (<1024)

**Linux Security** : Ports **< 1024** nécessitent **root**

**Exemples** :
- Port 80 (HTTP)
- Port 443 (HTTPS)
- Port 22 (SSH)
- Port 53 (DNS)
- Port 25 (SMTP)

---

### Sans sudo (Échoue)

```bash
docker run -d -p 80:80 nginx
```

**Erreur** :
```
Error response from daemon: driver failed programming external connectivity: 
Error starting userland proxy: listen tcp4 0.0.0.0:80: bind: permission denied
```

**Cause** : Port 80 nécessite root

---

### Avec sudo (Réussit)

```bash
sudo docker run -d -p 80:80 nginx
```

**Résultat** :
```
a1b2c3d4e5f6...
```

**Vérifier** :
```bash
sudo docker ps
# PORTS
# 0.0.0.0:80->80/tcp

curl http://localhost
# Page nginx
```

---

### Ports Non-Privilégiés (≥1024)

```bash
# Port 8080 : Pas besoin sudo
docker run -d -p 8080:80 nginx

# Port 3000 : Pas besoin sudo
docker run -d -p 3000:80 nginx
```

**💡 Recommandation** :
- **Développement** : Ports >1024 (8080, 3000, 8888...)
- **Production** : Ports <1024 avec sudo ou reverse proxy (Nginx/Traefik)

---

## 📋 Antisèche - Commandes Docker/Podman

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `docker --help` | Aide générale | `docker --help` |
| `docker cmd --help` | Aide commande | `docker rm --help` |
| `docker search img` | Chercher image | `docker search nginx` |
| `docker pull img` | Télécharger image | `docker pull nginx` |
| `docker pull img:tag` | Télécharger version | `docker pull nginx:alpine` |
| `docker images` | Lister images | `docker images` |
| `docker rmi img:tag` | Supprimer image | `docker rmi nginx:alpine` |
| `docker rmi --force img` | Forcer suppression | `docker rmi -f nginx` |
| `docker run img` | Démarrer conteneur | `docker run nginx` |
| `docker run -d img` | Démarrer détaché | `docker run -d nginx` |
| `docker run -p H:C img` | Mapper port | `docker run -p 8080:80 nginx` |
| `docker run --name nom img` | Nom personnalisé | `docker run --name web nginx` |
| `docker ps` | Lister conteneurs actifs | `docker ps` |
| `docker ps -a` | Lister tous conteneurs | `docker ps -a` |
| `docker stop cnt` | Arrêter conteneur | `docker stop web` |
| `docker start cnt` | Démarrer conteneur | `docker start web` |
| `docker rm cnt` | Supprimer conteneur | `docker rm web` |
| `docker rm -f cnt` | Forcer suppression | `docker rm -f web` |

---

## 🎓 Points Clés pour l'Examen

✅ **Conteneur** : Environnement isolé partageant noyau hôte (vs VM = OS complet)  
✅ **Podman** : Alternative Red Hat à Docker (daemonless, rootless, rétrocompatible)  
✅ **CentOS 8/RHEL 8** : `docker` → alias vers `podman`  
✅ **registries.conf** : Configure registres (`unqualified-search-registries`)  
✅ **nodocker** : `/etc/containers/nodocker` supprime warnings Podman  
✅ **docker search** : Chercher images dans registres  
✅ **docker pull** : Télécharger image (tag `latest` par défaut)  
✅ **docker pull img:tag** : Télécharger version spécifique  
✅ **docker images** : Lister images locales  
✅ **docker rmi** : Supprimer image (4 formats : nom:tag, nom, ID, repository complet)  
✅ **docker rmi --force** : Forcer suppression (arrête/supprime conteneurs utilisant image)  
✅ **docker run** : Créer + démarrer conteneur  
✅ **docker run -d** : Mode détaché (arrière-plan)  
✅ **docker run -p H:C** : Redirection port hôte:conteneur  
✅ **docker run --name** : Nom personnalisé conteneur  
✅ **docker ps** : Conteneurs actifs uniquement  
✅ **docker ps -a** : Tous conteneurs (actifs + arrêtés)  
✅ **docker stop** : Arrêter proprement (SIGTERM)  
✅ **docker start** : Démarrer conteneur existant arrêté  
✅ **docker rm** : Supprimer conteneur arrêté  
✅ **Well-known ports (<1024)** : Nécessitent `sudo` sur hôte  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Configuration Podman CentOS 8

```bash
# 1. Installer Podman
sudo dnf install podman

# 2. Vérifier alias docker
which docker
# /usr/bin/docker → /usr/bin/podman

# 3. Configurer Docker Hub uniquement
sudo nano /etc/containers/registries.conf
# unqualified-search-registries = ["docker.io"]

# 4. Supprimer warnings
sudo touch /etc/containers/nodocker

# 5. Tester
docker run -d --name test nginx
docker ps
# CONTAINER ID   IMAGE   NAMES
# ...            nginx   test
```

---

### Scénario 2 : Déployer Nginx Web Server

```bash
# 1. Chercher image
docker search nginx --filter is-official=true
# nginx   Official build...

# 2. Télécharger
docker pull nginx:alpine  # Version légère

# 3. Vérifier téléchargement
docker images nginx
# REPOSITORY   TAG      SIZE
# nginx        alpine   23.5MB

# 4. Démarrer (port 8080)
docker run -d -p 8080:80 --name web-server nginx:alpine

# 5. Vérifier
docker ps
# PORTS: 0.0.0.0:8080->80/tcp

# 6. Tester
curl http://localhost:8080
# <!DOCTYPE html>...

# 7. Arrêter
docker stop web-server

# 8. Supprimer
docker rm web-server
```

---

### Scénario 3 : Base de Données MySQL

```bash
# 1. Télécharger MySQL
docker pull mysql:5.7

# 2. Démarrer avec mot de passe root
docker run -d \
  -p 3306:3306 \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=secretpass \
  mysql:5.7

# 3. Vérifier logs
docker logs mysql-db
# MySQL init process done. Ready for start up.

# 4. Connexion depuis hôte
mysql -h 127.0.0.1 -u root -p
# Enter password: secretpass

# 5. Arrêter
docker stop mysql-db

# 6. Redémarrer (données persistent)
docker start mysql-db
```

---

### Scénario 4 : Nettoyer Environnement

```bash
# 1. Lister tout
docker ps -a

# 2. Arrêter tous conteneurs actifs
docker stop $(docker ps -q)

# 3. Supprimer tous conteneurs
docker rm $(docker ps -a -q)

# 4. Supprimer images non utilisées
docker image prune -a
# WARNING! This will remove all images without at least one container associated to them.
# Are you sure? [y/N] y

# 5. Vérifier
docker images
# REPOSITORY   TAG   IMAGE ID   CREATED   SIZE
# (vide)
```

---

### Scénario 5 : Application Multi-Conteneurs

```bash
# Stack Web : Nginx + Redis + MySQL

# 1. Réseau partagé
docker network create app-network

# 2. Base de données
docker run -d \
  --name db \
  --network app-network \
  -e MYSQL_ROOT_PASSWORD=root123 \
  -e MYSQL_DATABASE=appdb \
  mysql:5.7

# 3. Cache Redis
docker run -d \
  --name cache \
  --network app-network \
  redis:alpine

# 4. Web server
docker run -d \
  --name web \
  --network app-network \
  -p 80:80 \
  nginx:alpine

# 5. Vérifier stack
docker ps
# db      mysql:5.7
# cache   redis:alpine
# web     nginx:alpine

# 6. Tester connectivité inter-conteneurs
docker exec web ping -c 2 db
# PING db (172.18.0.2): 64 bytes from 172.18.0.2: seq=0
```

---

### Scénario 6 : Debugging Conteneur

```bash
# Conteneur web ne démarre pas

# 1. Vérifier logs
docker logs web-server
# Error: Port 80 already in use

# 2. Vérifier ports hôte
sudo netstat -tlnp | grep :80
# tcp  0.0.0.0:80  LISTEN  1234/nginx

# 3. Changer port conteneur
docker rm web-server
docker run -d -p 8080:80 --name web-server nginx

# 4. Accéder terminal conteneur
docker exec -it web-server /bin/bash
# root@a1b2c3d4:/# nginx -v
# nginx version: nginx/1.25.3

# 5. Inspecter config
docker inspect web-server | grep -A 10 "Mounts"
```

---

### Scénario 7 : Mise à Jour Image

```bash
# Mettre à jour nginx vers nouvelle version

# 1. Version actuelle
docker images nginx
# nginx   1.24   ...

# 2. Arrêter ancien conteneur
docker stop web-server
docker rm web-server

# 3. Télécharger nouvelle version
docker pull nginx:1.25

# 4. Supprimer ancienne image
docker rmi nginx:1.24

# 5. Démarrer nouvelle version
docker run -d -p 8080:80 --name web-server nginx:1.25

# 6. Vérifier
docker exec web-server nginx -v
# nginx version: nginx/1.25.0
```

---

### Scénario 8 : Port Privilégié avec sudo

```bash
# Déployer Nginx sur port 80 standard

# 1. Sans sudo (échoue)
docker run -d -p 80:80 nginx
# Error: permission denied

# 2. Avec sudo (réussit)
sudo docker run -d -p 80:80 --name web-prod nginx

# 3. Vérifier (sudo requis)
sudo docker ps
# PORTS: 0.0.0.0:80->80/tcp

# 4. Tester
curl http://localhost
# Welcome to nginx!

# Alternative : Reverse proxy
# 1. Nginx hôte (port 80, démarre avec systemd = root)
# 2. Conteneur (port >1024, ex: 8080)
# 3. Nginx hôte proxy → conteneur
```

---

### Scénario 9 : Persistance Données (Volumes)

```bash
# MySQL avec données persistantes

# 1. Créer volume
docker volume create mysql-data

# 2. Démarrer MySQL avec volume
docker run -d \
  --name mysql-prod \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=prod123 \
  -v mysql-data:/var/lib/mysql \
  mysql:5.7

# 3. Créer données
docker exec -it mysql-prod mysql -uroot -pprod123 -e "CREATE DATABASE testdb;"

# 4. Supprimer conteneur (données restent)
docker stop mysql-prod
docker rm mysql-prod

# 5. Recréer conteneur (données persistent)
docker run -d \
  --name mysql-prod-new \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=prod123 \
  -v mysql-data:/var/lib/mysql \
  mysql:5.7

# 6. Vérifier données
docker exec mysql-prod-new mysql -uroot -pprod123 -e "SHOW DATABASES;"
# testdb  ← Toujours présent !
```

---

### Scénario 10 : Automatisation avec Script

```bash
#!/bin/bash
# deploy-app.sh : Déployer stack application

set -e  # Arrêter si erreur

echo "🚀 Déploiement application..."

# Arrêter anciens conteneurs
echo "📦 Nettoyage anciens conteneurs..."
docker stop web db cache 2>/dev/null || true
docker rm web db cache 2>/dev/null || true

# Télécharger images
echo "⬇️ Téléchargement images..."
docker pull nginx:alpine
docker pull mysql:5.7
docker pull redis:alpine

# Créer réseau
echo "🌐 Création réseau..."
docker network create app-network 2>/dev/null || true

# Démarrer conteneurs
echo "▶️ Démarrage base de données..."
docker run -d \
  --name db \
  --network app-network \
  -e MYSQL_ROOT_PASSWORD=dbpass \
  mysql:5.7

echo "▶️ Démarrage cache..."
docker run -d \
  --name cache \
  --network app-network \
  redis:alpine

echo "▶️ Démarrage web server..."
docker run -d \
  --name web \
  --network app-network \
  -p 80:80 \
  nginx:alpine

echo "✅ Déploiement terminé !"
echo "📊 Conteneurs actifs :"
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"
```

**Usage** :
```bash
chmod +x deploy-app.sh
sudo ./deploy-app.sh
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Image Not Found

```
Unable to find image 'ngnix:latest' locally
docker: Error response from daemon: pull access denied for ngnix, 
repository does not exist or may require 'docker login'
```

**Cause** : Faute de frappe (`ngnix` au lieu de `nginx`)

**Solution** :
```bash
docker pull nginx  # Orthographe correcte
```

---

### Erreur 2 : Port Déjà Utilisé

```
Error starting userland proxy: listen tcp4 0.0.0.0:8080: bind: address already in use
```

**Cause** : Port 8080 déjà pris sur hôte

**Solution** :
```bash
# Trouver processus
sudo netstat -tlnp | grep :8080
# tcp  0.0.0.0:8080  LISTEN  1234/docker-proxy

# Arrêter conteneur conflictuel
docker ps | grep 8080
docker stop <container>

# OU changer port
docker run -p 8081:80 nginx
```

---

### Erreur 3 : Impossible Supprimer Image

```
Error response from daemon: conflict: unable to delete a1b2c3d4 (cannot be forced) 
- image is being used by running container e5f6a7b8
```

**Cause** : Image utilisée par conteneur running

**Solution** :
```bash
# 1. Arrêter conteneur
docker stop e5f6a7b8

# 2. Supprimer conteneur
docker rm e5f6a7b8

# 3. Supprimer image
docker rmi a1b2c3d4

# OU forcer (arrête/supprime conteneurs)
docker rmi --force a1b2c3d4
```

---

### Erreur 4 : Nom Conteneur Existe

```
Error response from daemon: Conflict. The container name "/web-server" is already in use by container "a1b2c3d4"
```

**Cause** : Nom déjà utilisé (même si conteneur arrêté)

**Solution** :
```bash
# Supprimer ancien conteneur
docker rm web-server

# Recréer
docker run --name web-server nginx
```

---

### Erreur 5 : Permission Denied (Podman)

```
ERRO[0000] cannot mkdir /run/user/0/libpod: mkdir /run/user/0: permission denied
```

**Cause** : Utiliser `sudo docker` avec Podman rootless

**Solution** :
```bash
# Ne PAS utiliser sudo avec Podman rootless
docker run nginx  # Sans sudo

# OU mode rootful
sudo podman run nginx
```

---

## 🔧 Commandes Bonus Utiles

### docker exec : Exécuter Commande dans Conteneur

```bash
# Bash interactif
docker exec -it web-server /bin/bash

# Commande unique
docker exec web-server nginx -v
# nginx version: nginx/1.25.3

# En tant que root
docker exec -u root web-server whoami
# root
```

---

### docker logs : Voir Logs Conteneur

```bash
# Logs complets
docker logs web-server

# Suivre logs (temps réel)
docker logs -f web-server

# Dernières 50 lignes
docker logs --tail 50 web-server

# Avec timestamps
docker logs -t web-server
```

---

### docker inspect : Inspecter Conteneur

```bash
# Toutes infos (JSON)
docker inspect web-server

# IP conteneur
docker inspect -f '{{.NetworkSettings.IPAddress}}' web-server
# 172.17.0.2

# Variables d'environnement
docker inspect -f '{{.Config.Env}}' web-server
```

---

### docker stats : Monitoring Ressources

```bash
# Utilisation ressources temps réel
docker stats

# CONTAINER ID   NAME         CPU %   MEM USAGE / LIMIT    MEM %
# a1b2c3d4e5f6   web-server   0.05%   5.2MiB / 15.6GiB     0.03%
```

---

**🎯 Prochaine étape** : Work on the Command Line (Module 3)

*Dernière mise à jour: 1 février 2026*
