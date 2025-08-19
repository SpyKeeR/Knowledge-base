# Console & Boot IOS**🧠 Console Cisco : Accès physique et couches OSI**

📸 **Matériel montré** :

• Ports console : RJ-45 classique + USB mini-B (nouveau format)

• Câbles : RS-232 vers RJ-45 + USB Type A vers mini-B



🔧 **Rôle du port console** :

• Permet la configuration initiale du périphérique Cisco

• Sert au **recovery des mots de passe**

• Accès hors ligne = pas besoin de réseau, seulement d’un accès **physique direct**



🧪 **Fonctionnement** :

• Utilisation via un logiciel de terminal (ex : **PuTTY**, **Tera Term**, etc.)

• Nécessite une configuration de session série : **9600 bauds**, 8 bits, no parity, 1 stop bit, no flow control

• Permet d'interagir avec l'**IOS** via la ligne de commande (CLI)



📶 **Couche OSI** concernée : **Couche 1 (Physique)**

→ Transmission série de données, sans encapsulation réseau



**🖥️ Boot & Accès à l’IOS Cisco**

🧾 **Contenu d’un boot Cisco IOS (extrait affiché)** :

• Affiche les tests de démarrage (POST), le chargement de l’IOS depuis la Flash

• Affiche la détection des interfaces matérielles

• Passage automatique en mode user ou setup selon la config



🗝️ **Accès à IOS** :

Deux méthodes principales :

• **Console** : Accès direct via câble console et terminal local

• **SSH** : Accès distant si interface SVI + IP + VTY + login configurés



🔐 Console = indispensable lors de la **première config** ou en cas de **perte d’accès réseau**
