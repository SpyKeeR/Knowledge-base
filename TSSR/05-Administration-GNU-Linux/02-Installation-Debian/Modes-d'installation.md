# Modes d'installation

## **📀 Installation depuis CD/DVD (méthode classique)**

- La plus ancienne, super fiable.
- Images dispo via HTTP, FTP, BitTorrent, ou Jigdo (très rapide mais pas encore parfait).
- Plusieurs types d'images selon les besoins.



### **🖥️ Debian Live (tester sans toucher au disque)**

- Boot direct depuis CD/USB sans rien installer.
- Teste ta machine → pilotes, compatibilité, etc.
- Si OK ➔ Icône "Installer" sur le bureau pour passer en dur.



#### **🎨 Environnements graphiques proposés**

- GNOME / KDE / XFCE / LXDE
- Dispo en 32 bits (i386) et 64 bits (AMD64)



### **📦 Image complète (CD/DVD)**

- Tous les paquets nécessaires sont présents.
- ⚠️ Attention : tu risques de télécharger plein de trucs inutiles.



### **🌐 Net Install (installation réseau)**

- Un petit ISO minimaliste.
- Télécharge uniquement ce dont tu as besoin pendant l'installation (plus léger et plus récent).



### **🔌 Installation via clé USB**

- Devenu la norme aujourd'hui !
- Outils recommandés : Ventoy, Rufus, Balena Etcher, Unetbootin.



## **📡 Boot PXE (réseau)**

- Boot sur le réseau sans support physique.
- Utilise DHCP pour récupérer une IP ➔ Télécharge le fichier d'amorçage par TFTP.



### **🛠️ Déploiement automatique (Industrialisation)**

- **FAI (Fully Automatic Installation)** : script + fichier de réponses(preseed) = zéro intervention.
- Clonage avec : CloneZilla, Fog, Symantec Ghost, Acronis TrueImage.
