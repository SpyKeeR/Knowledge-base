# Modèle OSI**🧱 Le modèle OSI – C’est quoi au juste ?**

Le **modèle OSI (Open System Interconnection)**, proposé par l’ISO, sert à **standardiser la communication entre deux systèmes sur un réseau**.

Il est divisé en **7 couches**, qui travaillent **de manière indépendante mais communicante**. Chacune joue un rôle précis dans le chemin des données, de l’app à la prise réseau.



**📦 Les PDU, SDU et PCI – Les unités de données**

- **PDU (Protocol Data Unit)** : unité de base utilisée à chaque couche pour encapsuler les données.
- **SDU (Service Data Unit)** : c’est la “donnée utile” reçue d’une couche supérieure.
- **PCI (Protocol Control Information)** : infos de contrôle (comme en-têtes ou métadonnées) ajoutées à la SDU pour former la PDU.

🧠 Chaque couche ajoute sa "propre sauce" à la donnée avant de la passer à la suivante.



**🧭 Les 7 couches du modèle OSI**

1.  **⚙️ Physique** (Layer 1) : transmission **bit par bit** sur le média (signal, connectique, voltage…)
2.  **🔗 Liaison de données** (Layer 2) : **communication directe entre machines** (adressage MAC, détection/correction d'erreurs)
3.  **🌐 Réseau** (Layer 3) : **acheminement entre réseaux** (routage, adresses IP)
4.  **📦 Transport** (Layer 4) : **fiabilité de bout en bout**, **découpage**, **contrôle de flux** (TCP/UDP)
5.  **🔐 Session** (Layer 5) : **gestion de session** (authentification, points de reprise)
6.  **🎨 Présentation** (Layer 6) : **traduction, chiffrement, compression** des données
7.  **🖥️ Application** (Layer 7) : **interface avec l’utilisateur**, **accès réseau** pour les applis

🧩 Astuce mnémotechnique : "Ça Peut Laisser Réellement Tout Système Planté" (de bas en haut) ou "All People Seem To Need Data Processing" (en anglais, de haut en bas)



**🧪 Exemples de protocoles par couche**

Quelques **protocoles connus** à associer aux couches (pour les repérer dans Wireshark plus tard) :

- 🧱 Couche 3 (Réseau) : **IP**, **ICMP**, **ARP**
- 📦 Couche 4 (Transport) : **TCP**, **UDP**
- 🧠 Couches 5 à 7 (Session → Application) : **HTTP**, **HTTPS**, **DNS**, **DHCP**, **FTP**, **SMTP**, etc.

🎯 Ces couches sont celles qu'on retrouve souvent quand on capture des paquets ou qu’on debug une appli web par exemple.



**🧩 Pourquoi c’est essentiel de bien connaître l’OSI ?**

Parce que ça te permet de :

- 🛠️ **Identifier où se situe un problème** réseau (câble, IP, port, appli… ?)
- 🔧 **Choisir les bons outils de test** (ping = couche 3, telnet = couche 7, etc.)
- 📚 **Comprendre les protocoles** que tu configures ou dépannes
- 🧰 **Traduire les erreurs** du terrain en concepts théoriques

🎓 Tous les techniciens réseaux sérieux ont **le modèle OSI dans la tête en permanence**, c’est **ta grille de lecture du réseau**.
