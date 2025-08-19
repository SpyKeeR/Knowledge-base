# Nom d'hôte**🧾 Qu’est-ce qu’un hostname ?**

Le **hostname** est le **nom de ta machine**.

Il peut être :

- 🧩 **Nom court** : identifie la machine localement (ex : srv-lan)
- 🌐 **FQDN (Fully Qualified Domain Name)** : nom complet, composé du nom court + domaine (ex : srv-lan.demo.eni)



**⚙️ Configuration du nom d’hôte**

Le nom d’hôte se règle ici :

- 📄 **Fichier /etc/hostname** → contient uniquement le nom court ou FQDN.
- 💡 Tu peux aussi utiliser la commande hostnamectl set-hostname <nom> (méthode moderne et propre !).

Exemple : hostnamectl set-hostname srv-lan.demo.eni



**📍 Résolution locale du nom d’hôte**

Le hostname **doit être résolvable** localement, même sans DNS :

- ➕ Ajoute une entrée dans **/etc/hosts** comme :

127.0.1.1 srv-lan.demo.eni srv-lan

Cela permet que ping srv-lan ou ping srv-lan.demo.eni retourne bien une IP locale.



**🕵️‍♂️ Vérification**

- 📤 La commande hostname affiche le nom d'hôte actuel.
- 🔁 hostnamectl status donne plus de détails : hostname statique, transient, pretty name…
