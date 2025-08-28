# **🔐 Protocole SSH – Base de l'administration distante**

SSH (Secure Shell) est un **protocole réseau sécurisé**, utilisé pour :

- Connexion **à distance** en ligne de commande
- **Chiffrement** des données échangées
- Authentification (mot de passe ou **clé publique/privée**)

💡 Le trafic SSH passe **par défaut** sur le port **TCP 22**.

Utilisé principalement pour **administrer des systèmes Unix/Linux** à distance.



## **💻 Côté client – Comment se connecter en SSH ?**

### **🐧 Sous Linux & macOS**

Client intégré : ssh (fourni par openssh-client)

➡️ Appel en terminal : ssh user@ip

### **🪟 Sous Windows 10+**

➡️ **Client SSH intégré**, activable via les *Fonctionnalités facultatives* :

→ Aller dans *Paramètres > Applications > Fonctionnalités facultatives*

→ Ajouter **"Client OpenSSH"** si non présent

→ Utilisable directement en ligne de commande PowerShell ou cmd avec la commande ssh

✅ Pratique, rapide, rien à installer, compatible avec les scripts !



### **🧰 Et les clients tiers ? (info bonus)**

Même si c’est moins utile aujourd’hui, tu pourrais rencontrer :

- **PuTTY** (ancien, minimaliste, ultra connu)
- **MobaXterm** (plus complet : multi-onglets, SFTP, X11, etc.)
- **mRemoteNG** (gestion multi-connexions, multi-protocoles)

📌 Mais dans un cadre pro/formation actuelle, **le client natif suffit largement**.

