# M01 - Administration Windows & Active Directory

## Chapitre 2 - Bases de gestion d'un domaine on-premises

---

## 2.1 Notions conceptuelles des services AD DS

### Workgroup vs Domaine AD

#### Groupe de travail (Workgroup)

- Chaque poste gère sa propre base de comptes locale (SAM)
- La zone de confiance est limitée au poste lui-même
- Pas de gestion centralisée, chaque poste est autonome

#### Domaine Active Directory

- Les systèmes membres du domaine **approuvent** le domaine pour l'authentification
- ✅ Authentification centralisée des utilisateurs
- ✅ Administration centralisée de tous les membres du domaine
- La base SAM locale existe toujours, mais le domaine prend la priorité pour l'authentification

### Le domaine AD

Les **contrôleurs de domaine** (DC) assurent :

- La gestion du domaine
- L'hébergement de la base AD
- L'authentification des utilisateurs et machines

Les systèmes clients peuvent être :

- **Membres du domaine** : approuvent les utilisateurs du domaine, régis par des règles communes (GPO, etc.)
- **Autonomes** : n'authentifient que les utilisateurs de la base locale (SAM)

### La forêt AD

- Contient **un ou plusieurs domaines** Active Directory
- Possède un **schéma unique** (classes d'objets et attributs disponibles)
- Les domaines sont liés par des **relations d'approbation transitives bidirectionnelles**

💡 Le schéma est partagé par toute la forêt. Une modification du schéma (ajout d'attribut, extension pour Exchange, etc.) impacte tous les domaines.

### Les sites AD

Les sites sont des **objets logiques** qui représentent la topologie physique du réseau :

- Composés d'un ou plusieurs **sous-réseaux TCP/IP**
- Servent à gérer la **réplication** des données entre contrôleurs distants
- Permettent la **localisation des ressources** (un client s'authentifie de préférence sur un DC de son site)

### Les contrôleurs de domaine

**Fonctionnement multi-maître** : tous les DC peuvent apporter des modifications aux services de domaine (sauf les rôles FSMO).

Les données AD sont stockées à deux endroits :

| Stockage | Chemin par défaut | Contenu |
| --- | --- | --- |
| **Base de données AD** (NTDS.dit) | `C:\Windows\NTDS` | Objets, attributs, structure |
| **Partage SYSVOL** | `C:\Windows\SYSVOL` | Scripts de logon, fichiers de GPO |

Les modifications sont propagées entre DC via la **réplication**.

### Structure logique de la base AD

La base AD est découpée en **3 partitions logiques** :

- 🗂️ **Domaine** : objets spécifiques au domaine (utilisateurs, groupes, ordinateurs, etc.)
- 🗂️ **Schéma** : classes d'objets et attributs disponibles (partagé par toute la forêt)
- 🗂️ **Configuration** : objets liés aux sites AD, partitions de la base

Des **partitions applicatives** peuvent s'y ajouter (ex : zones DNS intégrées à l'AD).

### Contrôleur de domaine en lecture seule (RODC)

Le RODC (Read-Only Domain Controller) a des particularités :

- Réplication **unidirectionnelle** : les données vont vers le RODC uniquement, jamais en sens inverse
- **Stratégie de réplication des mots de passe** : on définit quels mots de passe peuvent être mis en cache sur le RODC (et quels sont interdits)
- Possède un **compte administrateur local** (pas de droits sur le domaine)

⚠️ Cas d'usage : sites distants avec un niveau de sécurité physique insuffisant, ou des besoins d'authentification réduits. Le RODC permet une authentification locale sans exposer la base AD en écriture.

---

## 2.2 Les rôles FSMO

FSMO = **Flexible Single Master Operation**

5 rôles hébergés chacun sur **un seul contrôleur** à un instant donné :

### Rôles à l'échelle de la forêt (2)

| Rôle | Utilité |
| --- | --- |
| **Maître d'attribution des noms de domaine** | Contacté lors de l'ajout/suppression d'un domaine, de partitions applicatives, ou d'un renommage de domaine |
| **Maître de schéma** | Seul DC autorisé à **écrire** sur le schéma AD. Les modifications sont ensuite répliquées sur toute la forêt |

### Rôles à l'échelle du domaine (3)

| Rôle | Utilité |
| --- | --- |
| **Émulateur PDC** (CPD) | Reçoit en priorité les mises à jour de mots de passe. Gère les modifications GPO. Source de temps pour tous les DC du domaine |
| **Maître RID** | Alloue des plages d'identifiants relatifs (RID) à chaque DC. Le RID compose en partie le SID des objets |
| **Maître d'infrastructure** | Maintient à jour les références d'objets inter-domaines (contexte multi-domaines) |

### Répartition initiale

- Le **1er DC du domaine racine** de la forêt détient les **5 rôles**
- Le **1er DC d'un nouveau domaine** (enfant) reçoit les **3 rôles de domaine**

### Transfert et récupération

🔧 Identification des détenteurs actuels :

```powershell
# Rôles de la forêt
Get-ADForest | Select-Object *master

# Rôles du domaine
Get-ADDomain | Select-Object pdc*, *master
```

Deux opérations possibles :

- **Transfert** : le DC d'origine est encore joignable, on déplace proprement le rôle
- **Récupération (seize)** : le DC d'origine n'est plus joignable. ⚠️ L'ancien détenteur ne devra **plus jamais être remis en ligne**

⚠️ La console Schéma Active Directory n'est pas activée par défaut (pour raisons de sécurité). Une erreur de manipulation sur le schéma peut endommager irrémédiablement l'annuaire.

---

## 2.3 Le catalogue global

Un DC qui héberge des **informations de tous les domaines de la forêt** est appelé **serveur de catalogue global** (GC).

Ce qu'il faut retenir :

- Contient l'**intégralité des objets** de la forêt, mais seulement un **sous-ensemble d'attributs** pour chaque objet
- Ces informations sont stockées dans des **partitions logiques supplémentaires** en lecture seule
- 📌 Microsoft recommande de rendre **tous les DC serveurs de catalogue global**

Configuration :

- Lors de la promotion : option "Catalogue global" cochée par défaut
- En PowerShell : activé par défaut, désactivable avec `-NoGlobalCatalog`
- Modifiable après promotion dans la console **Sites et services AD** (propriétés NTDS Settings du DC)

---

## 2.4 Déploiement d'un domaine AD

### Scénarios de promotion

Avant de promouvoir un serveur, il faut connaître l'existant et le rôle souhaité :

| Scénario | Cmdlet PowerShell | Objectif |
| --- | --- | --- |
| Nouveau domaine dans une **nouvelle forêt** | `Install-ADDSForest` | Créer une forêt et son domaine racine |
| Nouveau domaine dans une **forêt existante** | `Install-ADDSDomain` | Nouvelle entité administrative avec ses propres objets |
| DC supplémentaire d'un **domaine existant** | `Install-ADDSDomainController` | Accroître la disponibilité des services de domaine |

### Niveaux fonctionnels

Les niveaux fonctionnels conditionnent les versions d'OS autorisées pour les DC et les fonctionnalités disponibles. Un niveau fonctionnel ne peut être qu'**augmenté**, jamais rétrogradé.

Niveaux disponibles : 2012, 2012 R2, 2016, 2019, 2022, 2025.

### Prérequis de promotion

Avant de promouvoir :

1. ✅ Nommage du poste correct (hostname définitif)
2. ✅ Adressage IP **statique** configuré
3. ✅ Installation du rôle AD DS

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

### Promotion en pratique

Paramètres à définir selon le scénario :

- Devenir serveur DNS (oui/non)
- Niveau fonctionnel de forêt/domaine
- Catalogue global (activé par défaut)
- Mot de passe DSRM (Directory Services Restore Mode)
- Configuration en RODC (optionnel)
- Emplacement des données AD

🔧 Exemple de promotion d'un DC supplémentaire :

```powershell
Install-ADDSDomainController -DomainName "domXX.ad" -InstallDns:$true -Credential (Get-Credential "DOMXX\administrateur")
```

### Dépromotion (retrait d'un DC)

```powershell
Uninstall-ADDSDomainController
```

⚠️ Avant de dépromouvoir, vérifier :

- Le DC ne détient **aucun rôle FSMO** (les transférer d'abord)
- D'autres **serveurs de catalogue global** existent
- D'autres **serveurs DNS** sont disponibles

💡 Pour un guide complet de décommission/remplacement de DC : <https://chader.fr/en/migrate-domain-controller-2022-2025/>

---

## 2.5 Gestion quotidienne de l'annuaire AD

### Outils de gestion

Trois outils principaux :

- **MMC Utilisateurs et ordinateurs Active Directory** (ADUC) : l'outil historique
- **Centre d'administration Active Directory** (ADAC) : interface plus moderne, basée sur PowerShell
- **Cmdlets PowerShell AD** : pour l'automatisation et les opérations en masse

### Objets utilisateurs

Chaque utilisateur est un objet unique dans l'annuaire, avec des attributs répartis sur plusieurs onglets :

- Infos générales (nom, prénom, poste, coordonnées)
- Options de compte (désactivation, expiration mot de passe, etc.)
- Paramètres Bureau à distance
- Appartenance aux groupes

🔧 Commandes PowerShell clés :

```powershell
# Rechercher des utilisateurs
Get-ADUser -Filter {SamAccountName -eq "dgrenier"}

# Créer un utilisateur
New-ADUser -Name "Kirk Hammett" -GivenName "Kirk" -Surname "HAMMETT" `
  -SamAccountName "khammett" -UserPrincipalName "khammett@domaine.ad" `
  -AccountPassword (ConvertTo-SecureString "Mercre10" -AsPlainText -Force) `
  -Enabled $true

# Modifier un utilisateur
Set-ADUser -Identity "khammett" -Title "Guitariste"

# Infos détaillées d'un utilisateur
Get-ADUser -Filter {SamAccountName -eq "dgrenier"} -Properties * | Select-Object *
```

### 🔧 Gestion des mots de passe et activation en masse

Pipeline PowerShell pour affecter un mot de passe et activer plusieurs comptes d'un coup :

```powershell
Get-ADUser -Filter {SamAccountName -eq "dgrenier" -or SamAccountName -eq "ivedere" -or SamAccountName -eq "curique"} `
  -SearchBase "OU=Utilisateurs,OU=domCM,DC=domCM,DC=ad" |
  Set-ADAccountPassword -NewPassword (ConvertTo-SecureString "Cs3cr3t!" -AsPlainText -Force) -Reset -PassThru |
  Enable-ADAccount
```

💡 Le `-PassThru` est la clé : il renvoie l'objet dans le pipeline pour que `Enable-ADAccount` puisse le récupérer.

### Import en masse depuis un fichier CSV

Méthodologie recommandée :

1. Créer un **compte de référence** manuellement
2. Exporter ses caractéristiques en CSV avec `Get-ADUser | Export-Csv`
3. S'en inspirer pour créer le fichier CSV d'import
4. Tester l'import d'un seul compte
5. Importer tous les comptes

🔧 Exemple d'import bulk depuis un fichier RH :

```powershell
Import-Csv -Path "C:\ListingADs\listing_rh.csv" -Delimiter ";" `
  -Header "Prénom","Nom","Service" -Encoding Default |
  Select-Object -Skip 1 |   # Sauter la ligne d'en-tête du CSV
  ForEach-Object {
    New-ADUser -Name "$($_.Prénom) $($_.Nom)" `
      -GivenName $_.Prénom -Surname $_.Nom `
      -SamAccountName "$($_.Prénom[0])$($_.Nom)".ToLower() `
      -Title $_.Service `
      -Path "OU=Utilisateurs,OU=domCM,DC=domCM,DC=ad"
  }
```

⚠️ Le fichier RH est souvent au format Excel. Il faut l'exporter en CSV (séparateur `;`) depuis Excel avant de l'utiliser avec `Import-Csv`.

### Objets ordinateurs

Les systèmes joints au domaine **s'authentifient aussi** (canal sécurisé entre le poste et le DC).

Attributs d'un objet ordinateur : nom, description, version OS, groupes d'appartenance, localisation.

Points importants :

- **Pré-création** possible : prédéfinir l'emplacement dans l'annuaire et cibler qui peut joindre la machine
- **Réinitialisation** : si remplacement du poste ou problème de canal sécurisé
- ⚠️ En cas de **clonage/duplication** de VM : toujours exécuter `sysprep` au préalable pour régénérer le SID

### Les groupes de domaine

Les groupes servent à appliquer des règles communes à leurs membres. Ils sont caractérisés par un **type** et une **étendue**.

#### Types de groupe

| Type | Usage |
| --- | --- |
| **Sécurité** | Octroyer/retirer des privilèges, positionner dans des ACL. Peut aussi servir de groupe de distribution |
| **Distribution** | Publipostage/messagerie uniquement (pas d'usage sécurité) |

#### Étendues de groupe

| Étendue | Membres acceptés | Utilisable sur | Convention de nommage | Cas d'usage |
| --- | --- | --- | --- | --- |
| **Global** | Objets du **même domaine** uniquement | Toute ressource du domaine ou de la forêt | `G-Commerciaux` | Regrouper par caractéristique commune (service, fonction) |
| **Universel** | Objets de **tout domaine** de la forêt | Toute ressource du domaine ou de la forêt | `U-Commerciaux` | Regrouper des groupes globaux inter-domaines |
| **Domaine Local** | Objets de **tout domaine** de la forêt | Uniquement dans le **domaine de création** | `DL CT sur Marketing` | Affecter des permissions sur une ressource précise |
| **Local** | N/A (poste uniquement) | Uniquement sur le **poste** d'appartenance | `Administrateurs` | Groupes natifs du système (hors domaine) |

📌 La stratégie recommandée par Microsoft est **AGDLP** : Account → Global → Domain Local → Permission. En mono-domaine, on peut simplifier en **AGLP**.

🔧 Commandes PowerShell pour les groupes :

```powershell
# Créer un groupe
New-ADGroup -Name "G-Intérimaires" -GroupScope Global -GroupCategory Security `
  -Path "OU=Groupes,OU=domCM,DC=domCM,DC=ad"

# Créer un groupe et ajouter un membre en une seule ligne (pipeline)
New-ADGroup -Name "G-Intérimaires" -GroupScope Global -GroupCategory Security `
  -Path "OU=Groupes,OU=domCM,DC=domCM,DC=ad" -PassThru |
  Add-ADGroupMember -Members "ctalmie"

# Lister les membres d'un groupe
Get-ADGroupMember -Identity "G-Informatique"

# Exporter les membres dans un fichier
Get-ADGroupMember -Identity G-Informatique |
  Out-File -FilePath C:\ListingADs\Mbres_Ginfo.txt

# Listing de tous les groupes globaux avec leurs membres
Get-ADGroup -Filter {GroupScope -eq "Global"} `
  -SearchBase "OU=Groupes,OU=domCM,DC=domCM,DC=ad" |
  Select-Object Name, @{N="Membres";E={
    (Get-ADGroupMember $_.DistinguishedName |
     Select-Object -ExpandProperty SamAccountName) -join ", "
  }} | Format-Table -AutoSize |
  Out-File -FilePath C:\ListingADs\ctrl_membres_groupes.txt
```

### Conteneurs et Unités d'Organisation

#### Conteneurs systèmes (par défaut)

| Conteneur | Contenu |
| --- | --- |
| **Builtin** | Groupes de domaine local créés par défaut (Administrators, Users, etc.) |
| **Computers** | Objets ordinateurs joints au domaine (emplacement par défaut) |
| **System** | Objets nécessaires au fonctionnement de l'AD |
| **Users** | Utilisateurs et groupes par défaut du domaine/forêt |

⚠️ On ne peut pas créer de conteneurs systèmes ni les personnaliser. Il est recommandé de créer ses objets **en dehors** de ces conteneurs par défaut.

#### Unités d'Organisation (OU)

Créées par l'administrateur pour :

- 🎯 L'application de **stratégies de groupe** (GPO)
- 🎯 La mise en oeuvre de **délégation administrative**
- 🎯 L'**organisation** des objets dans l'arborescence

📌 Un objet ne peut être positionné que dans **un seul conteneur** à la fois.

#### Exemple de structure d'OU (ateliers)

L'arborescence recommandée pour les ateliers suit 3 niveaux :

```text
domCM.ad
└── domCM                          # Niveau 1 : racine unique
  ├── Utilisateurs               # Niveau 2 : par catégorie d'objet
  │   ├── Direction              # Niveau 3 : par service
  │   ├── Informatique
  │   └── Comptabilité
  ├── Groupes
  ├── Stations de travail
  └── Serveurs
```

---

## 2.6 Configuration des sites Active Directory

### Éléments de gestion des sites

La configuration des sites se fait via la console **Sites et services Active Directory** et implique :

- **Sites** : représentation logique d'un emplacement physique
- **Liens de sites** : connexion logique entre sites (par défaut : `DefaultIPSiteLink`)
- **Réseaux (subnets)** : sous-réseaux IP associés à un site
- **Contrôleurs de domaine** : affectés à un site

### Réplication intrasite vs intersite

| | Intrasite | Intersite |
| --- | --- | --- |
| **Fréquence** | Permanente et quasi-instantanée (< 1 min) | Planifiable, selon le lien de site |
| **Objets connexion** | Créés par paires (symétrique) | Gérés selon la topologie de liens |
| **Mise à jour** | Automatique à l'ajout/suppression d'un DC | Selon configuration |

Le **KCC** (Knowledge Consistency Checker) est le service qui gère la topologie de réplication sur chaque DC. Il crée et met à jour automatiquement les objets connexion.

🔧 Outil de diagnostic et d'action sur la réplication :

```powershell
# Commandes repadmin
repadmin /replicate      # Déclencher une réplication
repadmin /syncall        # Synchroniser avec tous les partenaires
repadmin /showrepl       # Afficher l'état de la réplication
repadmin /replsummary    # Résumé de l'état de réplication
repadmin /kcc            # Forcer le recalcul de la topologie par le KCC

# Vérifier le site d'appartenance d'un poste
nltest /DSGETSITE
```

---

## 2.7 Atelier 2 - Création du domaine AD

### Promotion de CD1

CD1 est promu premier DC du domaine `domNN.ad` (NN = initiales du stagiaire).

```powershell
# Étape 1 : installer le rôle AD DS
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Étape 2 : promouvoir en tant que premier DC (nouvelle forêt)
Install-ADDSForest -DomainName "domCM.ad"
```

### Ajout de CD2 (DC supplémentaire)

Pour assurer la **tolérance de panne** (recommandation Microsoft : minimum 2 DC) :

| Paramètre | Valeur |
| --- | --- |
| OS | Windows Server 2022 |
| CPU / RAM / Disque | 1 CPU / 4 Go / 40 Go |
| Réseau | Host-only |
| IP | 192.168.21.2 |

💡 CD2 peut être créé par **clonage** de CD1 (ne pas oublier le `sysprep` !).

```powershell
# Promotion en DC supplémentaire
Install-ADDSDomainController -DomainName "domCM.ad" -InstallDns:$true `
  -Credential (Get-Credential "DOMCM\administrateur")
```

### Intégration des membres au domaine

Joindre W11-CL1 et SRV1 au domaine créé.

### Installation des RSAT sur le poste client

Les **RSAT** (Remote Server Administration Tools) permettent d'administrer les serveurs et services AD directement depuis le poste de travail, sans bureau à distance.

### Répartition des rôles FSMO

Par défaut, CD1 détient les 5 rôles. Pour limiter le risque de perte totale :

- Transférer le rôle **Maître RID** et **Émulateur PDC** vers CD2
- Vérifier la répartition avec `Get-ADForest | Select-Object *master` et `Get-ADDomain | Select-Object pdc*, *master`

---

## 2.8 Atelier 3 - Gestion d'un domaine AD

### Création des comptes utilisateurs

Convention de nommage : 1ère lettre du prénom + nom (ex: `dgrenier` pour David Grenier).

| Prénom | Nom | Service | Poste | Particularité |
| --- | --- | --- | --- | --- |
| David | Grenier | Direction | Directeur Comptabilité Finances | Tél: 504 |
| Isabelle | Védère | Informatique | Administratrice SR | Tél: 666 |
| Ivan | Tard | Informatique | Support technique | |
| Christelle | Urique | Comptabilité | Comptable | |
| Christophe | Talmie | Comptabilité | Comptable intérimaire | Compte désactivé au 31/12 |

Tous les comptes sont créés **désactivés** et **sans mot de passe**.

### Création des groupes globaux de sécurité

| Groupe | Membres |
| --- | --- |
| G-Comptabilité | Christelle, Christophe |
| G-Direction | David (+ adresse mail <direction@domNN.fr>) |
| G-Informatique | G-Support technique, Isabelle |
| G-Support technique | Ivan |
| G-Intérimaires | Christophe (créé en PowerShell) |

### Requêtes enregistrées

Dans la console ADUC, conteneur "Requêtes enregistrées" : créer une requête **"Comptes désactivés"** pour lister les comptes utilisateurs désactivés.

💡 Fonctionnalité peu connue mais très pratique pour le quotidien.

### Projet d'évolution : sites AD

Préparation d'un site secondaire suite au rachat d'un concurrent :

- Créer le site AD **"Agence"**, renommer le site par défaut en **"Siege"**
- Associer le sous-réseau `172.16.0.0/16` au site Agence
- Vérifier les enregistrements DNS relatifs aux sites
- Vérifier l'appartenance du client au bon site avec `nltest /DSGETSITE`

⚠️ Aucun DC n'est déplacé ni installé à ce stade.

---

## 📌 À retenir

1. **AD DS centralise l'authentification** et l'administration. En Workgroup, chaque poste gère sa propre base SAM isolée.

2. **5 rôles FSMO** répartis sur 2 niveaux (2 forêt + 3 domaine). Les répartir sur plusieurs DC pour éviter un SPOF. Commande clé : `Get-ADForest | Select *master` et `Get-ADDomain | Select pdc*,*master`.

3. **Tous les DC devraient être catalogue global** (recommandation Microsoft). Le GC contient tous les objets de la forêt avec un sous-ensemble d'attributs.

4. **3 types d'étendue de groupe** à connaître : Global (regrouper par caractéristique), Domaine Local (affecter des permissions), Universel (inter-domaines). Stratégie AGDLP.

5. **Les OU structurent l'AD** pour les GPO, la délégation et l'organisation. Un objet ne peut être que dans une seule OU. Les conteneurs systèmes (Builtin, Computers, Users) ne sont pas personnalisables.

6. **`-PassThru`** est indispensable dans les pipelines PowerShell AD pour chaîner les opérations (créer + activer, créer groupe + ajouter membres).
