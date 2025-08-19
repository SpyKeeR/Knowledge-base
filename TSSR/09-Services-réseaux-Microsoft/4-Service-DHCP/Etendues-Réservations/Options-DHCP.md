# Options DHCP**⚙️ Qu’est-ce qu’une option DHCP ?**

Les **options DHCP** permettent d’ajouter des **paramètres IP complémentaires** transmis aux clients avec leur bail 📦

➡️ Objectif : automatiser la config réseau des postes (DNS, passerelle, suffixe DNS, WINS, etc.)



**🧾 Exemples d’options fréquentes**

Quelques options utiles :

- 🧭 **Passerelle par défaut** → Option 3 = *Router*
- 🌍 **Serveur DNS** → Option 6 = *Domain Name Server*
- 🏷️ **Nom de domaine** → Option 15 = *Domain Name*
- 🎯 Et bien d'autres : WINS, NetBIOS, PXE, etc.

👉 Dans l'interface MMC, clic droit sur l’étendue ou le serveur > *Configurer les options* > coche les cases > saisis les IP → Valide ✅



**🎨 Personnalisation poussée**

Le DHCP propose **des dizaines d’options** prêtes à l’emploi 🔧

💡 Certains scénarios avancés (WDS, VoIP, VPN…) utilisent des options spécifiques → vues en TP plus tard



**🧩 Où définit-on ces options ?**

Les options peuvent être **appliquées à différents niveaux**, selon la granularité souhaitée :

1.  🏢 **Au niveau du serveur** : affecte **toutes les étendues**
2.  📦 **Au niveau d’une étendue** : s’applique **uniquement à cette étendue**
3.  🔒 **Au niveau d’une réservation** : uniquement pour la **machine réservée**

➡️ Les options les plus spécifiques (réservation) **écrasent** celles plus générales (étendue ou serveur)
