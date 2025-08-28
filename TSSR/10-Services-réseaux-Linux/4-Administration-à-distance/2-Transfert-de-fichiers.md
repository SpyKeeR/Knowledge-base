# **📁 Transfert de fichiers sécurisé via SSH**

Le protocole **SSH** permet aussi de **transférer des fichiers chiffrés**, via la commande scp.

## ➡️ **scp = Secure Copy**

→ Fonctionne dans **les deux sens** (client > serveur / serveur > client)

→ Syntaxe : `scp source destination`

Exemple :

→ Télécharger un fichier du serveur : `scp user@ip:/chemin/fichier /destination/local`

→ Envoyer un fichier vers le serveur : `scp fichier user@ip:/chemin/distant`



### 📦 **SCP & Permissions**

- Option -p ➜ préserve : date modif, heure, permissions et ownership du fichier (⚠️ niveau inode)



## **🐧 Côté Linux/macOS**

scp est déjà intégré via le paquet openssh-client.



## **🪟 Côté Windows 10+**

🚀 Bonne nouvelle : **scp est intégré nativement** si le *Client OpenSSH* est activé (via fonctionnalités facultatives).

➡️ Tu peux l’utiliser directement dans **cmd ou PowerShell**, comme sur Linux.

→ Pas besoin de logiciel tiers !



## **🧰 Clients graphiques alternatifs (bonus)**

Si tu préfères une interface visuelle ou pour les transferts en masse :

- **WinSCP** : simple, efficace, explorateur type FTP
- **MobaXterm** : inclus SFTP automatique dès que tu ouvres une session SSH

✅ Pratique mais à privilégier en dépannage ou pour certaines manips visuelles.

