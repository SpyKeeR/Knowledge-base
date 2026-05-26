# M01 - Administration Windows & Active Directory
## Chapitre 4 - Les stratégies de groupe (GPO)

---

## 4.1 Fonctionnement des GPO

### Objectifs et enjeux

Les stratégies de groupe visent à :
- Réduire le **TCO** (coût total de possession) et améliorer le **ROI**
- Réduire les tâches d'administration en contexte de domaine (configurer N postes depuis un seul endroit)
- Simplifier le déploiement d'applications

⚠️ Contraintes à garder en tête :
- Les paramètres pris en charge **dépendent de la version** du système client
- Le nombre de paramètres disponibles est **très élevé** (des milliers)
- La configuration peut vite devenir complexe

### Du registre aux stratégies

Sous Windows, la majorité des points de configuration sont stockés dans le **registre** (base de données hiérarchique).

Les 5 ruches principales :

| Ruche | Contenu |
|---|---|
| `HKEY_CLASSES_ROOT` | Infos sur les applications (OLE, extensions de fichiers) |
| `HKEY_CURRENT_USER` | Configuration de l'utilisateur connecté |
| `HKEY_LOCAL_MACHINE` | Configuration spécifique à l'ordinateur |
| `HKEY_USERS` | Tous les profils utilisateur chargés |
| `HKEY_CURRENT_CONFIG` | Profil matériel en cours |

La GPO permet de modifier le registre de manière centralisée et structurée, sans aller éditer les clés manuellement sur chaque poste.

### Stratégie de groupe vs stratégie locale

| | Stratégie de groupe | Stratégie locale |
|---|---|---|
| **Contexte** | Nécessite un domaine AD | Utilisable partout (domaine ou workgroup) |
| **Portée** | S'applique sur un ensemble d'objets | Se configure poste par poste |
| **Priorité** | ✅ L'emporte en cas de conflit | Écrasée par la GPO de domaine |

### Stockage et application

- Les GPO sont stockées sur les DC dans le **partage SYSVOL**
- Les clients récupèrent les GPO localement via les **CSE** (Client-Side Extensions)
- Les CSE intègrent et appliquent les paramètres sur le poste client

### Intervalles d'actualisation

| Cible | Fréquence de rafraîchissement |
|---|---|
| Postes clients / serveurs membres | Toutes les **90 minutes** (± 30 min de jitter) |
| Contrôleurs de domaine | Toutes les **5 minutes** |

🔧 Forcer la mise à jour immédiate :

```powershell
gpupdate          # Rafraîchir les GPO
gpupdate /force   # Forcer la réapplication complète
```

### Exemples de paramètres courants

- Uniformiser l'aspect du bureau, du menu Démarrer, du navigateur
- Bloquer l'accès aux périphériques USB
- Limiter l'accès à des applications ciblées
- Configurer le pare-feu Windows
- Déployer des imprimantes, des logiciels, des scripts
- Rediriger des dossiers utilisateurs

---

## 4.2 Ciblage des stratégies

### Liaison des GPO

Une GPO peut être liée à 3 types de conteneurs :
- 🌐 **Domaine** (s'applique à tous les objets du domaine)
- 📁 **Unités d'Organisation** (ciblage fin)
- 🏢 **Sites Active Directory** (ciblage géographique)

📌 Les GPO s'appliquent aux objets **ordinateurs** et **utilisateurs** présents dans le conteneur ciblé (ou ses enfants). Elles ne s'appliquent **pas** directement aux objets groupes ni à leurs membres.

### Console de gestion

La **GPMC** (Group Policy Management Console / `gpmc.msc`) est l'outil central pour créer, lier, gérer et diagnostiquer les GPO.

### Cumul et conflits

Quand un objet est soumis à **plusieurs GPO** :
- Les paramètres **distincts** se **cumulent**
- Les paramètres **identiques avec des valeurs différentes** créent un **conflit**

En cas de conflit, c'est la **dernière GPO appliquée** qui l'emporte ("la plus proche de l'objet").

### 📌 Ordre d'application : LSDOU

L'ordre d'application des GPO suit la règle **LSDOU** :

```
L → S → D → OU

Local    Site    Domaine    Unité d'Organisation
```

1. **L**ocale : stratégie locale du poste
2. **S**ite : GPO liées au site AD
3. **D**omaine : GPO liées au domaine
4. **OU** : GPO liées aux OU (du parent vers l'enfant)

➡️ La **dernière appliquée** est celle qui prévaut. Les GPO d'OU enfant l'emportent sur celles du domaine, qui l'emportent sur celles du site, etc.

💡 Au sein d'un même conteneur, les GPO avec le **numéro d'ordre le plus élevé** sont appliquées en premier (et donc potentiellement écrasées par celles de numéro d'ordre inférieur).

### Héritage et restrictions

L'héritage se propage du parent vers l'enfant. Plusieurs mécanismes permettent de contrôler l'application :

**Blocage d'héritage**
- S'active sur une OU
- Annule la prise en compte de **toutes** les GPO héritées des conteneurs parents
- ⚠️ Impact global : bloque tout, pas sélectif

**Filtres de sécurité**
- Via des ACE (ACL) : autoriser ou refuser la lecture/application d'une GPO pour des groupes spécifiques
- Permet un ciblage plus fin que la simple liaison

**Filtres WMI**
- Limitent l'application selon des critères obtenus via des requêtes WMI
- Ex : appliquer uniquement aux postes Windows 11, ou aux portables, etc.

**Appliqué / Renforcé (Enforced)**
- Rend la GPO **prioritaire** et outrepasse le blocage d'héritage
- À utiliser avec parcimonie

**État de la GPO**
- Activée, désactivée, ou désactivation partielle (paramètres utilisateur OU ordinateur seulement)
- 💡 Désactiver la partie non utilisée d'une GPO accélère le traitement côté client

### Diagnostic et troubleshooting

Quand un paramètre ne s'applique pas comme prévu :

| GPO appliquée mais paramètre inopérant | GPO non appliquée |
|---|---|
| Paramètre non pris en charge par l'OS client | Problème de liaison |
| Conflit avec une autre GPO (priorité) | Filtrage de sécurité qui bloque |
| | Filtre WMI qui exclut le poste |

🔧 Outils de diagnostic :

```powershell
# Depuis le poste impacté
gpresult /r             # Résumé des GPO appliquées
gpresult /h gpreport.html   # Rapport détaillé en HTML (très pratique pour le troubleshoot)
```

💡 Le rapport HTML de `gpresult /h` est bien plus lisible que la sortie texte. Il montre clairement quelles GPO sont appliquées, lesquelles sont filtrées, et pourquoi.

Depuis la GPMC : assistant **Résultats de stratégie de groupe** (RSoP) pour simuler ou analyser l'application sur un utilisateur/ordinateur donné.

---

## 4.3 Les modèles d'administration

### Périmètre des GPO

Les GPO couvrent 4 grands domaines :
- 📦 **Déploiement** (logiciels, imprimantes)
- 📜 **Scripts** (logon, logoff, startup, shutdown)
- 🔒 **Sécurité** (pare-feu, audit, stratégies de compte)
- ⚙️ **Paramétrages système et applications** (registre, préférences)

### Les modèles d'administration (ADMX/ADML)

Les modèles d'administration regroupent par catégories les paramètres configurables dans les GPO.

- Les modèles système sont **natifs** (Windows, composants Windows, réseau, etc.)
- D'autres peuvent être ajoutés : Microsoft Office, Mozilla Firefox, Google Chrome, etc.

Chaque modèle est composé de **2 fichiers** :
- `.admx` : contient les paramètres (format XML)
- `.adml` : fichier de langue associé (descriptions localisées)

### Stockage : local vs magasin central

| Mode | Emplacement | Portée |
|---|---|---|
| **Local** | `C:\Windows\PolicyDefinitions` | Uniquement le poste d'édition |
| **Magasin central** | `SYSVOL\<domaine>\Policies\PolicyDefinitions` | Tous les postes qui éditent des GPO |

📌 Le magasin central est la bonne pratique en entreprise. Il garantit que **tous les administrateurs** (quel que soit le poste d'édition) voient les mêmes paramètres disponibles.

💡 Pour le configurer : copier le contenu de `C:\Windows\PolicyDefinitions` (ADMX + dossiers de langue ADML) dans le chemin SYSVOL correspondant. Dès que le magasin central existe, il prend le pas sur le local.

---

## 4.4 Les stratégies de sécurité et de compte

### Stratégies de compte

Les stratégies de compte sont **uniques à l'échelle du domaine** et définies par défaut dans la **Default Domain Policy**.

Elles contiennent notamment la **stratégie de mot de passe** du domaine :
- Longueur minimale
- Complexité requise
- Historique (nombre d'anciens mots de passe mémorisés)
- Durée de vie maximale/minimale
- Verrouillage de compte (seuil, durée, réinitialisation du compteur)

### Stratégies de mot de passe affinées (Fine-Grained Password Policies)

Pour appliquer des règles de mot de passe **différentes selon les groupes** (ex : règles plus strictes pour les admins), on utilise les **PSO** (Password Settings Objects).

⚠️ Les PSO ne se configurent **pas via GPO** mais via :
- Le **Centre d'administration Active Directory** (ADAC)
- Ou directement dans l'annuaire : `System > Password Settings Container`

Chaque PSO définit des paramètres de mot de passe et le **groupe de sécurité** auquel ils s'appliquent. Le PSO avec la **valeur de priorité la plus basse** l'emporte en cas de conflit.

---

## 4.5 Atelier 5 - Mise en oeuvre de stratégies de groupe

### GPO pour tous les utilisateurs

| Paramètre | Objectif |
|---|---|
| Ne pas afficher le dernier identifiant à l'ouverture de session | Sécurité : empêcher de connaître les noms d'utilisateur |
| Forcer l'activation du pare-feu (connexions entrantes) | Sécurité réseau |
| Ajouter une règle entrante pour autoriser le ping (ICMP) | Permettre le diagnostic réseau malgré le pare-feu |

### GPO pour la Direction uniquement

| Paramètre | Objectif |
|---|---|
| Masquer les propriétés du Poste de travail (clic droit) | Restriction d'interface |
| Bloquer la console Certificats dans MMC | Empêcher l'ajout de ce composant |
| Bloquer les outils d'édition du registre | Empêcher `regedit` / `regedt32` |
| Bloquer l'accès au Gestionnaire des tâches | Restriction de diagnostic |

### GPO pour G-Intérimaires

| Paramètre | Objectif |
|---|---|
| Masquer la corbeille sur le bureau | Restriction d'interface |
| Ne pas placer les fichiers supprimés dans la corbeille | Suppression directe |
| Bloquer l'exécution des scripts PowerShell | Sécurité : empêcher l'exécution de scripts |
| Fond d'écran non modifiable | Uniformisation du bureau |
| Barre des tâches non déplaçable | Cohérence de l'interface |

### Stratégie de mots de passe renforcée

Modification de la Default Domain Policy :
- Longueur minimale : **10 caractères**
- Historique : **20 derniers** mots de passe mémorisés
- Durée de vie maximale : **120 jours**

### Stratégie de mot de passe affinée (Bonus)

Pour G-Informatique, via un PSO :
- Verrouillage au bout de **2 tentatives** échouées
- Longueur minimale : **15 caractères**
- Seul un administrateur peut déverrouiller un compte

### Déploiement d'imprimantes par GPO

Déploiement automatique des imprimantes Dell 5210CN selon l'appartenance au service (via la console Gestion de l'impression ou la GPMC).

### Magasin central des modèles d'administration

Configurer le magasin central pour que les modèles d'administration soient disponibles depuis n'importe quel poste d'édition (y compris le client Windows avec RSAT).

### Fonctionnalités avancées par GPO

| Besoin | Méthode |
|---|---|
| G-Support technique : droits de modification réseau sur toutes les stations | GPO → Groupes restreints ou Préférences GPO (groupes locaux) |
| Redirection du dossier "Mes Documents" vers `\\SRV1\...\C:\Base` (sauf intérimaires) | GPO → Redirection de dossiers (User Configuration > Policies > Windows Settings > Folder Redirection) |
| Déploiement de 7zip "à la demande" | GPO → Déploiement de logiciels (package MSI, mode "publié" pour laisser le choix) |
| Activation Bureau à distance + NLA sur tout nouveau serveur membre | GPO ordinateur → Paramètres de Bureau à distance (niveau difficile) |

💡 Pour la redirection de dossiers de façon sécurisée, Microsoft documente les bonnes pratiques : https://learn.microsoft.com/en-us/troubleshoot/windows-server/user-profiles-and-logon/create-security-enhanced-redirected-folder

---

## 4.6 Atelier Bonus - DC supplémentaire en Server Core

### Objectif

Ajouter un 3ème contrôleur de domaine **CD3** en mode **Server Core**, entièrement en PowerShell :

| Paramètre | Valeur |
|---|---|
| OS | Windows Server 2022 (Core) |
| Nom | CD3 |
| Site AD | Agence |
| IP | 172.16.0.1/16 |

Promotion en DC supplémentaire du domaine existant.

### Fonctions supplémentaires

- Ajouter CD3 dans le **Gestionnaire de serveur** de CD1 (gestion centralisée à distance)
- Depuis CD1, déployer le rôle **Serveur Web (IIS)** sur CD3 à distance
- Ajouter CD3 dans **Windows Admin Center**

### Bonus supplémentaires

- **Décommissionner** le DC Windows 2022 et le remplacer par un DC sous le dernier Windows Server
- Configurer la stratégie de mot de passe affinée pour G-Informatique (PSO)

---

## 4.7 🛠️ Outils complémentaires

Outils tiers recommandés pour l'audit et le reporting GPO/AD :

| Outil | Usage | Lien |
|---|---|---|
| **GPOZaurr** | Reporting et nettoyage de GPO (PowerShell) | https://github.com/EvotecIT/GPOZaurr |
| **PingCastle** | Audit de sécurité des GPO et de l'AD | https://www.pingcastle.com/ |
| **ORADAD** | Audit de sécurité AD (outil ANSSI) | https://github.com/ANSSI-FR/ORADAD |

---

## 📌 À retenir

1. **Ordre d'application LSDOU** : Local → Site → Domaine → OU. La dernière GPO appliquée l'emporte en cas de conflit. Les GPO se cumulent quand il n'y a pas de conflit.

2. **Les GPO ne ciblent pas les groupes** : elles se lient à des domaines, OU ou sites. Pour cibler un groupe spécifique, on utilise les **filtres de sécurité** ou on place les objets dans une OU dédiée.

3. **`gpresult /h gpreport.html`** est l'outil de troubleshoot GPO le plus efficace. Le rapport HTML montre clairement quelles GPO sont appliquées, filtrées, et pourquoi.

4. **Le magasin central** (`SYSVOL\...\PolicyDefinitions`) garantit que tous les administrateurs voient les mêmes modèles d'administration. Sans lui, chaque poste d'édition utilise ses propres modèles locaux.

5. **Les stratégies de compte** (mot de passe, verrouillage) sont uniques par domaine via la Default Domain Policy. Pour des règles différentes par groupe, utiliser les **PSO** (Fine-Grained Password Policies) via ADAC, pas les GPO.

6. **Appliqué/Enforced** outrepasse le blocage d'héritage. Le blocage d'héritage est brutal (bloque tout). Les filtres de sécurité et WMI permettent un ciblage plus chirurgical.
