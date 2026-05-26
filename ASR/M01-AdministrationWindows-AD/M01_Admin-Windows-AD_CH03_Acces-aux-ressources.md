# M01 - Administration Windows & Active Directory
## Chapitre 3 - L'accès aux ressources

---

## 3.1 Les ressources et autorisations

Ce chapitre couvre deux types de ressources principales : les **services de fichiers** et les **services d'impression**. L'accès à ces ressources doit respecter des contraintes de disponibilité et de sécurité.

### Autorisations NTFS

Les autorisations NTFS définissent des **privilèges d'accès aux dossiers et fichiers** sur les volumes formatés en NTFS ou ReFS. Elles s'appliquent à toute entité disposant d'un SID (utilisateurs, groupes de sécurité).

Principes fondamentaux :
- Les autorisations sont basées sur des **règles explicites** : Autoriser ou Refuser
- Toujours cibler des **groupes de sécurité** plutôt que des utilisateurs individuels
- Un utilisateur soumis à plusieurs règles bénéficie du **cumul des autorisations**
- ⚠️ Le **refus est toujours prioritaire** sur l'autorisation

Configuration via l'onglet **Sécurité** des propriétés du dossier/fichier.

### Deux niveaux de gestion des privilèges NTFS

**Autorisations de base** (les plus courantes) :

| Autorisation | Description |
|---|---|
| Lecture | Voir le contenu des fichiers et dossiers |
| Lecture + exécution | Lecture + lancer des programmes |
| Liste du contenu | Voir le contenu d'un dossier (dossiers uniquement) |
| Écriture | Créer des fichiers/dossiers, modifier des fichiers |
| Modification | Lecture + Écriture + Suppression |
| Contrôle Total | Tous les droits, y compris modifier les autorisations et s'approprier |

**Autorisations avancées** : parcours de dossier, lecture/écriture des attributs étendus, suppression de sous-dossiers et fichiers, modification des autorisations, appropriation...

💡 Les autorisations de base sont en réalité des combinaisons prédéfinies d'autorisations avancées. Quand une autorisation de base ne correspond pas exactement au besoin, on passe par les autorisations avancées.

### Héritage des autorisations

Les autorisations **s'héritent** du dossier parent vers les objets enfants. Le comportement est contrôlable :
- ✂️ **Désactivation** de l'héritage possible en un point de l'arborescence
- 🛡️ Depuis un dossier parent, on peut empêcher l'application aux objets enfants

📌 Impact du déplacement/copie sur les autorisations NTFS :

| Action | Même partition | Entre partitions/disques |
|---|---|---|
| **Déplacement** | Conservation des autorisations | Héritage (nouvelles autorisations du parent) |
| **Copie** | Héritage | Héritage |

⚠️ Piège classique : un déplacement au sein de la même partition **conserve** les autorisations d'origine. Si on veut appliquer l'héritage du nouveau parent, il faut le forcer manuellement.

---

## 3.2 Le partage de fichiers

### Principe

Le partage rend des fichiers **accessibles via le réseau**. Les partages fournissent un espace commun à plusieurs utilisateurs.

### Autorisations de partage

Les autorisations de partage sont **distinctes** des autorisations NTFS. Elles se configurent au niveau du partage lui-même.

3 niveaux de privilèges seulement :

| Autorisation | Description |
|---|---|
| Lecture | Voir les fichiers et dossiers |
| Modification | Lecture + créer/modifier/supprimer |
| Contrôle Total | Modification + gérer les autorisations |

Mêmes règles que NTFS : cumul des autorisations et refus prioritaire.

### 📌 Autorisations résultantes (la règle clé)

Quand un utilisateur accède à une ressource partagée depuis le réseau, **deux filtres** s'appliquent successivement :

1. D'abord les autorisations de **partage**
2. Puis les autorisations **NTFS**

➡️ **Les privilèges les plus restrictifs prévalent** entre les deux.

💡 En pratique, la bonne approche est de mettre les autorisations de partage assez larges (ex : "Tout le monde" en Contrôle Total ou Modification) et de faire le travail fin de sécurité au niveau **NTFS**. Ça simplifie la gestion et évite les conflits.

### Outils de gestion des partages

**En graphique :**
- Composant intégré au **Gestionnaire de serveur** (Services de fichiers et de stockage)
- Console MMC **Dossiers partagés** (`fsmgmt.msc`)
- Directement depuis les propriétés du dossier cible (onglet Partage)

🔧 **En PowerShell :**

```powershell
Get-SmbShare          # Lister les partages
New-SmbShare          # Créer un partage
Set-SmbShare          # Modifier un partage
Remove-SmbShare       # Supprimer un partage
```

---

## 3.3 Stratégie d'accès aux ressources partagées (AGDLP)

### Le modèle d'imbrication des groupes

Microsoft préconise l'imbrication **AGDLP** pour gérer efficacement l'accès aux ressources :

```
A → G → DL → P

Account        Groupe       Groupe de         Permission
(utilisateur)  Global       Domaine Local     (sur la ressource)
```

| Étape | Élément | Rôle |
|---|---|---|
| **A** | Compte utilisateur | L'utilisateur qui a besoin de l'accès |
| **G** | Groupe Global | Regroupe les utilisateurs par caractéristique commune (service, fonction) |
| **DL** | Groupe de Domaine Local | Représente un niveau de privilège sur une ressource précise |
| **P** | Permission | L'autorisation NTFS/partage affectée au groupe DL |

### 🎯 Exemple concret

Pour donner à Pénélope (rédactrice) le droit de modifier le dossier partagé "Articles" :

```
Pénélope  →  G-Rédaction  →  DL Modification sur Articles  →  Permission Modification
   (A)           (G)                  (DL)                          (P)
```

Sur la ressource "Articles", l'ACL contient :
- `DL Modification sur Articles` → Modification
- `Utilisateurs authentifiés` → Contrôle Total (partage uniquement, sécurité fine en NTFS)

💡 L'avantage : quand un nouveau rédacteur arrive, on l'ajoute simplement au groupe Global `G-Rédaction`. Pas besoin de toucher aux ACL des ressources.

---

## 3.4 Le partage d'imprimantes

### Service d'impression centralisé

L'ajout du service de rôle **Serveur d'impression** permet de :
- Partager des imprimantes sur le réseau
- Centraliser la gestion de l'impression depuis un serveur unique

### Vocabulaire Microsoft

⚠️ Terminologie spécifique à retenir :

| Terme Microsoft | Ce que c'est concrètement |
|---|---|
| **Périphérique d'impression** | Le matériel physique (ce qu'on appelle "imprimante" dans la vie courante) |
| **Imprimante** | L'objet logique configuré dans Windows pour gérer l'impression |

Relations possibles :
- Généralement 1 imprimante logique ↔ 1 périphérique physique
- Plusieurs imprimantes logiques → 1 même périphérique (ex : files d'impression différentes avec des priorités différentes)
- 1 imprimante logique → plusieurs périphériques (pool d'impression)

### Console de gestion

La console MMC **Gestion de l'impression** (`printmanagement.msc`) centralise :
- Gestion des **pilotes** installés
- Gestion des **ports** locaux ou TCP/IP
- Gestion des **paramètres** des imprimantes
- **Déploiement** des imprimantes par stratégie de groupe

### Autorisations d'impression

| Autorisation de base | Autorisation avancée |
|---|---|
| Imprimer | Imprimer |
| Gérer les documents | Gérer les documents |
| Gérer l'imprimante | Autorisation de lecture |
| | Modifier les autorisations |

### Déploiement des imprimantes

Trois méthodes :
- 🖐️ **Manuel** : ajout direct sur le poste client
- 📜 **Script** : automatisation via script de logon
- 🎯 **GPO** : déploiement via stratégies de groupe (méthode recommandée)

Le déploiement par GPO se configure depuis la console **Gestion de l'impression** ou depuis la **GPMC** (l'interface diffère mais le résultat est le même). Il faut préciser la GPO à utiliser et le type d'objet ciblé (utilisateur et/ou ordinateur).

---

## 3.5 La délégation administrative sur l'annuaire

### Principe

La délégation administrative permet de **confier des tâches d'administration ciblées** à du personnel approprié, sur des **conteneurs ciblés** (OU) du domaine.

Exemples classiques :
- Un responsable de service peut **réinitialiser les mots de passe** de son équipe
- Le support technique peut **créer et modifier des comptes** utilisateurs (sauf pour le service Informatique)

### Mise en oeuvre

Chaque objet de l'annuaire AD dispose d'une **ACL**. La délégation s'appuie sur l'ajout de privilèges ciblés à un groupe de sécurité sur une OU.

🔧 Procédure :
1. Clic droit sur une OU dans la console ADUC → **Délégation de contrôle**
2. Sélectionner le **groupe de sécurité** désigné
3. Choisir les **tâches à déléguer** (réinitialiser les MDP, créer/supprimer des comptes, etc.)

💡 La liste des tâches proposées par l'assistant est définie dans `C:\Windows\System32\delegwiz.inf`. Ce fichier peut être modifié pour ne proposer que les tâches souhaitées.

### Fournir un outil adapté

Une fois la délégation configurée, il faut donner un outil au personnel concerné. La méthode classique :

1. Créer une **nouvelle MMC vide** (`mmc.exe`)
2. Ajouter le composant enfichable **Utilisateurs et ordinateurs AD**
3. Se positionner sur l'OU déléguée → "Nouvelle fenêtre à partir d'ici"
4. Créer une **vue de liste de tâches** personnalisée
5. Ajouter les tâches déléguées à la liste
6. **Enregistrer** la MMC et la rendre accessible (partage, GPO, etc.)

⚠️ La console RSAT (ADUC) doit être installée sur les postes depuis lesquels la MMC personnalisée sera lancée.

---

## 3.6 Atelier 4 - Gestion de l'accès aux ressources

### Configuration du stockage (Storage Spaces)

Ajout de **3 disques SCSI de 40 Go** sur SRV1, gérés via les **Pools de stockage** :

- Tolérance de panne configurée (perte d'un disque sans interruption → miroir)
- Deux volumes créés :

| Volume | Taille | Format | Montage |
|---|---|---|---|
| DATA | 50 Go | NTFS | `E:\` |
| USERS | Le reste | NTFS | `C:\Base` |

💡 Si la création via la console graphique échoue → passer en PowerShell.

### Configuration des partages (AGDLP en action)

Trois partages à configurer, chacun illustrant le modèle AGDLP :

**📁 Documentation** (`E:\DATA\Documentation`)

| Entité | Privilège |
|---|---|
| G-Intérimaires | Aucun accès |
| Tous les utilisateurs | Lecture |
| G-Informatique | Contrôle Total |

Le partage doit être **listé dans l'annuaire Active Directory** (option "Publier ce partage dans AD").

**📁 Comptabilité** (`E:\DATA\Services\Comptabilité` → partage `Compta`)

| Entité | Privilège |
|---|---|
| Autres services | Aucun accès |
| G-Comptabilité | Modification |
| G-Informatique | Contrôle Total |

**📁 Informatique** (`E:\DATA\Informatique` → partage `Info`)

| Entité | Privilège |
|---|---|
| Tous les utilisateurs | Aucun accès |
| G-Informatique | Contrôle Total |

⚠️ Le partage `Info` doit être **invisible** (nom terminé par `$` → `Info$`).

Pour chaque partage : créer les **groupes de Domaine Local** nécessaires, y imbriquer les groupes globaux, puis affecter les permissions.

### Tests de validation

Les tests se font depuis le **poste client** avec différents comptes utilisateurs pour vérifier les accès en autorisation et en refus sur Documentation et Comptabilité.

### Délégation administrative

| Qui | Quoi | Où |
|---|---|---|
| David (G-Direction) | Réinitialiser les MDP, gérer les comptes (créer/modifier/supprimer) | OU Comptabilité |
| Ivan (G-Support technique) | Gérer les comptes utilisateurs | Tous les services **sauf** Informatique |
| Isabelle | Membre d'un groupe nativement privilégié sur tout l'annuaire | Tout l'annuaire |

💡 Pour Isabelle, le groupe natif est probablement **Admins du domaine** ou **Administrateurs de l'entreprise**.

### Gestion d'imprimantes

Partage d'une imprimante réseau Dell 5210CN (`192.168.21.30/16`) via SRV1 comme serveur d'impression :

- Pilote : **Dell Open Print PCL 5** (32 et 64 bits)
- Type de périphérique : **TCP/IP Generic Network Card**
- Test de déploiement manuel sur le poste client

Autorisations configurées :
- Tous les utilisateurs → Imprimer
- G-Comptabilité → Imprimer **uniquement entre 20h00 et 6h00** (restriction horaire)
- David (responsable Comptabilité) → Gérer la file d'attente
- G-Informatique → Contrôle Total

📌 L'imprimante doit être **référencée dans l'annuaire AD** (facilite la recherche pour les utilisateurs).

---

## 📌 À retenir

1. **Deux couches de sécurité** pour les accès réseau : autorisations de partage puis autorisations NTFS. Les **plus restrictives** entre les deux prévalent. En pratique, gérer la sécurité fine au niveau NTFS.

2. **AGDLP** (Account → Global → Domain Local → Permission) est la stratégie d'imbrication des groupes recommandée par Microsoft. Ça sépare la gestion des utilisateurs (groupes globaux) de la gestion des ressources (groupes de domaine local).

3. **Refus prioritaire** : dans le cumul des autorisations (NTFS ou partage), un refus explicite l'emporte toujours sur une autorisation.

4. **L'héritage NTFS** se propage du parent vers les enfants. Un déplacement au sein de la même partition conserve les autorisations d'origine (piège courant).

5. **La délégation administrative** permet de distribuer des tâches d'administration ciblées sur des OU spécifiques via l'assistant de délégation de contrôle. Toujours fournir un outil adapté (MMC personnalisée) au personnel à qui on délègue.

6. **Les partages cachés** se terminent par `$` (ex : `Info$`). Ils ne sont pas visibles lors d'un browse réseau mais restent accessibles par chemin UNC direct.
