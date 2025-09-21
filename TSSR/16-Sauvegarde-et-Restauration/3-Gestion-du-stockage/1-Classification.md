# Classification

La classification des sauvegardes permet de **gérer efficacement les données** selon :

- Leur **importance**
- Leur **fréquence d’accès**
- Leur **rapidité de restauration attendue**

Elle contribue à :

- 🔒 La sécurité des données
- 📈 L’optimisation des ressources de stockage
- 🔁 La disponibilité et la durabilité des informations



## ⚡ **1. Inline Backup** *(traitement en temps réel)*

### 🧠 **Principe** :

La sauvegarde inline applique des **optimisations (ex. : déduplication)** directement **pendant le processus de sauvegarde**.

### 💡 **Exemple** :

Une entreprise sauvegarde ses bases de données avec un logiciel qui **supprime les doublons en temps réel**, réduisant ainsi **l’espace de stockage utilisé**.



## 🌐 **2. Online Backup** *(disponible en permanence)*

### 🧠 **Principe** :

Les sauvegardes online sont **immédiatement accessibles**, souvent stockées sur des **systèmes actifs** comme des NAS ou serveurs.

### 💡 **Exemple** :

Une entreprise e-commerce sauvegarde chaque jour ses bases de transactions sur un **NAS**, permettant une **restauration instantanée** en cas de problème. Les employés peuvent y accéder directement.



## 🕓 **3. Near-line Backup** *(stockage intermédiaire)*

### 🧠 **Principe** :

Les données sont **moins accessibles que l’online**, mais restent **disponibles sans délai majeur**. Stockées sur des disques peu sollicités.

### 💡 **Exemple** :

Un cabinet d’avocats conserve des dossiers clients sur des disques **near-line** : peu utilisés, mais disponibles rapidement si besoin pour un audit ou une consultation.



## 📼 **4. Offline Backup** *(archivage déconnecté)*

### 🧠 **Principe** :

Les sauvegardes sont stockées sur **des supports déconnectés du réseau**, comme des **bandes magnétiques**, **disques externes** ou en centre d’archivage.

### 💡 **Exemple** :

Une université archive ses sauvegardes annuelles sur **bandes stockées hors site**, utilisées uniquement à des **fins de conformité ou historiques**.
