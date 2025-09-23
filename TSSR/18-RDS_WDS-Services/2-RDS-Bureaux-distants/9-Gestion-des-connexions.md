# Gestion des connexions

## 📍 **Accès à la gestion des connexions**

### 🔧 Depuis le **Gestionnaire de serveur** :

- Aller dans **Services Bureau à distance > Collections**
- Sélectionner la **collection souhaitée**
- Accéder à l’onglet **Connexions**

👥 Toutes les **sessions utilisateur actives** sont listées

🖱️ Un clic droit sur un utilisateur permet d’accéder aux actions disponibles

## ⚙️ **Actions possibles sur une connexion active**

### 🔌 **1. Déconnexion de l'utilisateur**

- Coupe l'accès à l'utilisateur
- La session reste **active sur le serveur**
- L’utilisateur peut se reconnecter ultérieurement et retrouver son environnement intact

### 💬 **2. Envoi de message à l’utilisateur**

- Permet d’informer l’utilisateur en cas d’intervention imminente
- Pratique en l’absence de contact téléphonique

### 🕹️ **3. Prise en main à distance (shadowing)**

- Mode **observation** : visualiser la session sans interaction
- Mode **interactif** : prise de contrôle pour **assistance technique**
- Utile en cas de **blocage logiciel ou support utilisateur**

### ❌ **4. Fermeture de la session**

- Termine la session et **interrompt tous les processus en cours**
- Libère les **ressources système** côté serveur
- À utiliser en cas de session inactive ou bloquée

### 🔄 **5. Réinitialisation de la session**
- Redémarre la session utilisateur
- Utile en cas de **problème logiciel** nécessitant un redémarrage

## 📌 **Résumé**

La gestion fine des connexions RDS via le gestionnaire de serveur permet aux administrateurs de :

- Surveiller les sessions en temps réel 👀
- Intervenir efficacement sur les connexions actives 🛠️
- Optimiser les ressources serveur 💡
- Garantir un service fluide aux utilisateurs 🧑‍💼