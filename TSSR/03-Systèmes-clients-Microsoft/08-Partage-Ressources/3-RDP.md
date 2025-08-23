# RDP

## 🔹 **Qu'est-ce que le Bureau à distance ?**

✅ Permet d’accéder **à un PC distant** comme si on était en local

✅ Utilisé pour la **maintenance des postes & serveurs**, ou l'accès à son **PC pro via VPN**

### ⚠️ **Limites** :

❌ **1 seule session simultanée** sur un poste client

❌ **2 sessions simultanées** sur un serveur

✅ **Plus de 2 sessions sur serveur** → Nécessite **Remote Desktop Services (RDS)**

### 🔹 **Alternatives pour le support en temps réel** :

🛠️ **Microsoft Remote Assistance** (outil natif)

🛠️ **AnyDesk, TeamViewer, VNC**



## **⚙️ Connexion à un Bureau à distance**

### 📌 **Depuis Windows**

➡️ Outil **Connexion Bureau à distance (mstsc.exe)**

➡️ Options avancées : **Sauvegarde des paramètres, redirection des ressources locales (USB, imprimante, presse-papier, etc.)**

### 📌 **Depuis la ligne de commande**

➡️ mstsc /v:NomDuPC (connexion rapide)

➡️ mstsc /admin (mode administrateur)



## **🔧 Configuration du poste distant (cible)**

### 📌 **Activer le Bureau à distance**

➡️ **Paramètres système avancés → Utilisation à distance**

➡️ Par défaut, seuls les **administrateurs** peuvent s’y connecter

➡️ Ajouter d'autres utilisateurs via le groupe **"Utilisateurs du Bureau à distance"**

### 📌 **Authentification**

🔹 **Standard** : Compatible avec tous les clients RDP

🔹 **Network Level Authentication (NLA)** : Authentification renforcée avant établissement du lien vers le bureau, évite le bruteforce 🔐

### 📌 **Protocole & Sécurité**

🔸 **Protocole : RDP (Remote Desktop Protocol)**

🔸 **Port utilisé : 3389** (doit être ouvert si connexion via réseau externe)

🔸 **Sécurité** : Active automatiquement les règles dans le **pare-feu Windows**

✅ **Bonne pratique** : Toujours **activer NLA** et **utiliser un VPN** pour sécuriser les connexions à distance 🌐🔒
