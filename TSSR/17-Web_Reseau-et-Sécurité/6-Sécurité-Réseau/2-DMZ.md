# 🌐 **Introduction à la DMZ (Demilitarized Zone)**

Une **DMZ** est un **sous-réseau physique ou logique** servant à **exposer les services accessibles publiquement** (ex. : site web, mail, DNS) tout en les **isolant du réseau interne**.

🎯 **Objectif principal** : Ajouter une **couche de sécurité** supplémentaire entre Internet (non sécurisé) et le réseau local (critique).



## 🧱 **Avantages principaux d’une DMZ**

- 🔐 **Sécurité renforcée** : empêche l’accès direct au LAN
- 🔗 **Segmentation du réseau** : cloisonne les services publics
- 🧾 **Contrôle d’accès** plus précis et plus rigide
- 🕵️ **Protection contre la reconnaissance réseau** depuis Internet
- 📋 **Conformité réglementaire** (ex : RGPD, ISO)
- 🧪 **Flexibilité** pour intégrer de nouvelles technologies
- 🚫 **Prévention de l'usurpation IP**
- 🚧 **Zone tampon** pour limiter les dégâts en cas de compromission



## 📉 **Impacts sur la performance réseau**

- ⏱️ **Latence légèrement accrue** due aux contrôles intermédiaires
- 🔄 **Charge plus importante** sur les pare-feu
- 🧩 **Complexité accrue** dans la conception et la maintenance
- 💻 **Besoin de ressources matérielles et humaines supplémentaires**
- 📈 **Optimisation du routage et des flux** requise
- 🧍‍♂️ **Isolation stricte** des services publics pour limiter les risques



## 🧰 **Services typiquement placés en DMZ**

- 🌍 **Serveurs Web** (HTTP/HTTPS)
- 📧 **Serveurs de messagerie** ou **brokers mail**
- 🧾 **Serveurs DNS**
- 🔄 **Serveurs Proxy**
- 📤 **Serveurs FTP**
