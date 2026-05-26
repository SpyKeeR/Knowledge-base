# M01 - Administration Windows & Active Directory
## Chapitre 1 - Les contextes de travail & Gestion de serveurs Windows

---

## 1.1 Les contextes de travail

Trois grands modèles d'hébergement coexistent aujourd'hui. Le choix impacte directement la façon d'administrer, de sécuriser et d'authentifier.

### On-premises (traditionnel)

Toutes les ressources (données, applications, services) sont hébergées sur des serveurs physiques ou virtualisés **internes à l'entreprise**. L'organisation maîtrise l'intégralité de la stack, du matériel à l'applicatif.

### Cloud

Les services et applications sont hébergés dans un ou plusieurs datacenters de **prestataires tiers** (Azure, AWS, GCP...). L'entreprise consomme des ressources à la demande sans gérer le matériel sous-jacent.

### Hybride

Mix des deux approches : certaines données/applications restent on-premises, d'autres sont déportées dans le cloud. C'est le modèle le plus courant aujourd'hui en entreprise.

### Modes d'administration selon le contexte

Les outils et méthodes d'administration varient selon le modèle :

- **Cloud / hybride** : portail web (ex : portail Azure), API, CLI cloud
- **On-premises** : client lourd, consoles MMC, PowerShell
- **Hybride** : combinaison des deux

📌 Les contextes d'authentification diffèrent aussi :

- **On-premises** : contrôleurs de domaine Active Directory (AD DS)
- **Cloud** : Entra ID (anciennement Azure AD)
- **Hybride** : synchronisation via **Azure AD Connect** entre AD DS on-prem et Entra ID

---

## 1.2 Gestion de serveurs Windows

### Éditions Windows Server (WS 2025)

| | Essentials | Standard | Datacenter |
|---|---|---|---|
| **Licence** | Inclus pour 25 users max (ou 50 devices) | CAL requises, 1 licence = 16 coeurs | CAL requises, 1 licence = 16 coeurs |
| **Hyper-V** | 1 seule VM tolérée | 2 VMs | VMs illimitées |
| **Cible** | Petites entreprises | PME sans besoin de virtualisation massive | Environnements hautement virtualisés |
| **Extras** | Fonctionnalités standard | Fonctionnalités standard | Fonctionnalités complémentaires (SDN, etc.) |

### Modes d'installation

**Installation complète ("expérience de bureau")**
- Interface graphique complète déployée
- Accès aux consoles de gestion des services
- Usage interactif classique

**Installation minimale (Server Core)**
- ⚠️ Sélectionnée **par défaut** lors de l'installation
- Environnement graphique très limité (pas d'explorateur, pas de MMC locale)
- Besoins matériels réduits
- Surface d'attaque amoindrie (moins de composants = moins de vulnérabilités)
- Administration à distance (PowerShell, RSAT, WAC)

💡 En production, Server Core est recommandé pour les rôles d'infrastructure (DC, DNS, DHCP). L'expérience de bureau se justifie pour les serveurs nécessitant des applications GUI (serveur de fichiers avec console, serveur d'impression...).

### Outils de gestion

**Gestionnaire de serveur (Server Manager)**
- Console centrale d'administration Windows Server
- Vue d'ensemble des rôles, fonctionnalités, événements et performances
- Point d'entrée pour ajouter/supprimer des rôles et fonctionnalités

**Interfaces en ligne de commande**
- `PowerShell` : langage de script évolué, standard pour l'administration moderne
- `cmd` : invite de commandes héritée, encore utile pour certaines opérations legacy

**Consoles MMC (Microsoft Management Console)**
- Consoles enfichables spécialisées (DNS, DHCP, AD Users & Computers, etc.)
- Utilisables en local ou à distance via les **RSAT** (Remote Server Administration Tools)

**Windows Admin Center (WAC)**
- Console de gestion **web en HTTPS**, apparue en 2018
- Package MSI à déployer sur un serveur (typiquement un serveur membre)
- Prend en charge différentes versions de Windows Server
- S'appuie sur les composants MMC existants
- Intégration d'outils Azure
- ⚠️ Nécessite un abonnement pour certaines fonctionnalités avancées

### Outils de contrôle et performances

Le suivi de l'état du système est essentiel pour :
- Garantir la **disponibilité** des services
- Identifier la source de **dysfonctionnements** (troubleshooting)
- **Optimiser** l'utilisation des ressources

#### Outils temps réel

**Gestionnaire de tâches**
- 🔧 Raccourci : `Ctrl+Shift+Echap`
- Inventaire des applications et processus en cours
- Vue rapide des performances : CPU, mémoire, disque, réseau
- Liste des utilisateurs connectés et des services

**Moniteur de ressources**
- Affichage plus détaillé que le Gestionnaire de tâches
- Détails par processus sur chaque composant (CPU, mémoire, disque, réseau)
- Utile pour identifier quel processus consomme quoi précisément

#### Outil d'analyse

**Analyseur de performances (Performance Monitor)**
- Collecte de données de performance sur des compteurs ciblés
- Analyse de données collectées sur une période donnée
- Création de rapports exploitables

💡 Permet de créer des **ensembles de collecteurs de données** planifiés pour comparer le comportement d'un serveur dans le temps (baseline).

#### Observateur d'événements (Event Viewer)

Centralise les messages provenant du système, des services et des applications.

| Journal | Contenu |
|---|---|
| **Application** | Événements rapportés par des applications |
| **Sécurité** | Événements liés aux actions d'audit (connexions, accès, etc.) |
| **Système** | Événements concernant l'OS |
| **Journaux dédiés** | Certains rôles disposent de journaux spécifiques (AD DS, DNS, etc.) |

Fonctionnalités complémentaires :
- **Vues personnalisées** : filtrage et regroupement d'événements ciblés, exportables
- **Abonnements** : collecte d'événements provenant d'autres serveurs (centralisation)
- **Actions déclenchées** : exécution automatique d'une action à la survenue d'un événement

---

## 1.3 Atelier 1 - Création de l'infrastructure

### Infrastructure de maquettage

Trois VMs sous VMware Workstation, toutes en réseau **host-only** :

| Machine | OS | CPU | RAM | IP |
|---|---|---|---|---|
| **CD1** | Windows Server 2022 | 2 | 4 Go | 192.168.21.1 |
| **SRV1** | Windows Server 2022 | 1 | 4 Go | 192.168.21.10 |
| **W10-CL1** | Windows 10 | 1 | 2 Go | 192.168.21.20 |

Réseau LAN : `192.168.0.0/16`

### 💡 Bypass du compte Microsoft à l'installation Windows 11

Trois méthodes possibles durant la phase OOBE (Out-Of-Box Experience) :

```
# Méthode 1 : durant la phase specialize OOBE
SHIFT + F10 → oobe\bypassnro

# Méthode 2 : durant la phase specialize OOBE
SHIFT + F10 → start ms-cxh:localonly

# Méthode 3 : sur l'écran du choix de région (OOBE)
Ctrl+Shift+J → WinJS.Application.restart("ms-cxh://LOCALONLY")
```

### Mesure des performances (baseline)

Collecte via l'Analyseur de performances sur CD1 :

| Objet | Compteurs |
|---|---|
| **Processeur** | % temps d'utilisation, % temps d'inactivité |
| **Mémoire** | Taille disponible (Mo), pages/s (échanges RAM/swap) |
| **Disque physique** | Temps moyen lecture, temps moyen écriture, temps requêtes R/W, temps d'inactivité |

Configuration de la collecte :
- Fichier stocké dans `C:\PerfLogs`, nommé `CompteurBase-AAMMJJ`
- Exécution planifiée : démarrage dans 30 min, durée de 10 min
- Répétition quotidienne jusqu'au vendredi inclus

### Déploiement de Windows Admin Center

WAC est déployé sur **SRV1** (serveur membre, pas le DC) :
- Installation via le `.msi` fourni, paramètres par défaut
- Accès via navigateur : `https://srv1:443`
- Activation du Bureau à distance sur SRV1 depuis la console WAC
- Ajout de CD1 à la liste des serveurs gérés

### Analyse post-collecte

- Ouverture du rapport généré dans l'Analyseur de performances
- Vérification que les valeurs collectées sont cohérentes (pas d'anomalie sur la baseline)
- Recherche dans l'**Observateur d'événements** des traces d'installation de WAC (date, heure, version)

---

## 📌 À retenir

1. **Trois modèles d'hébergement** : on-premises, cloud, hybride. Le modèle hybride domine aujourd'hui, avec AD DS on-prem synchronisé vers Entra ID via Azure AD Connect.

2. **Server Core est le mode par défaut** à l'installation de Windows Server. Il réduit la surface d'attaque et les besoins matériels.

3. **Windows Admin Center** est la console web moderne pour administrer les serveurs Windows. Elle se déploie sur un serveur membre et centralise la gestion.

4. **L'Analyseur de performances** permet de créer des baselines planifiées pour comparer le comportement d'un serveur dans le temps. Indispensable avant tout changement majeur.

5. **L'Observateur d'événements** centralise les logs système/application/sécurité et permet la création de vues personnalisées, d'abonnements inter-serveurs et de déclencheurs automatiques.
