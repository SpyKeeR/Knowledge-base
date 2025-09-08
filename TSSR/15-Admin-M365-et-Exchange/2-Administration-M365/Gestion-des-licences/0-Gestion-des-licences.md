# Gestion des licences

La gestion des licences dans **M365** est essentielle pour garantir que chaque utilisateur dispose des bons services : **Exchange, OneDrive, Teams, Office, Intune,** etc…



## **🧩 Plans de licence principaux**

**📧 Exchange Online (autonome ou inclus dans d'autres licences)**

- **Plan 1** :
  - 50 Go de boîte aux lettres
  - Archivage local 50 Go max
- **Plan 2** :
  - 100 Go de boîte aux lettres
  - Archivage illimité (par défaut limité à 100 Go, extensible par "auto-expansion")

## **🧰 Licences Microsoft 365 Business**

| **Licence** | **Contenu** |
|----|----|
| **Business Basic** | Exchange Online, Teams, SharePoint, OneDrive (web only) |
| **Business Standard** | Basic + Office (Word, Excel, Outlook, etc. en local) |
| **Business Premium** | Standard + Intune + Microsoft Defender for Business |
| **Apps for Business** | Pack Office seul (Word, Excel, etc.), sans services cloud |

🔸 **Limites** : max 300 utilisateurs par licence Business.



## **🏢 Licences Microsoft 365 Enterprise**

| **Licence** | **Contenu** |
|----|----|
| **E3** | Exchange Plan 2, Office ProPlus, OneDrive 1To+, SharePoint, Teams, Intune de base |
| **E5** | E3 + Microsoft Defender for 365, sécurité avancée, téléphonie Teams (Phone System) |
| **F3 (Frontline)** | Version allégée pour utilisateurs terrain (pas d’Office full, stockage réduit) |

🔸 Sans limite d’utilisateurs



## **⚙️ Gestion des licences dans le centre d'administration**

Dans `admin.microsoft.com`, tu peux :

- ➕ **Ajouter / supprimer** une licence à un utilisateur
- 📊 **Visualiser** les licences restantes, attribuées, expirées
- 📍 **Définir la zone géographique** (UsageLocation)
- 🔁 **Automatiser** avec des groupes dynamiques et des stratégies

## **💻 Fonctionnalités incluses (selon plan choisi)**

- Boîte aux lettres Exchange (taille selon licence)
- Archivage local et online (selon plan)
- Partage de calendriers, dossiers publics
- Outils de migration
- Protection anti-malware et antispam (Defender)
- Gestion des appareils (MDM, Intune)
- Outils de conformité (DLP, eDiscovery, Legal Hold…)
- Intégration AD hybride (via Entra Connect)



## **⚠️ Enjeux d’une bonne gestion**

### 🚨 **Risques d’erreurs ou surcoûts** :

- Licences **achetées mais non utilisées** ➜ gaspillage
- Mauvais plan attribué ➜ fonctionnalités absentes
- Aucune licence ➜ utilisateur bloqué
- Attribution manuelle non suivie ➜ chaos à grande échelle

### ✅ Une **bonne gestion des licences** permet :

- Des coûts optimisés
- Une conformité RGPD et sécurité respectée
- Une meilleure expérience utilisateur

