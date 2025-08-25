# Procédure d'install

## **🧱 Les bases indispensables**

Pour installer Debian, il faut **au minimum deux partitions** :

- **/ (racine)** 📂 : point de départ du système (FHS)
- **swap** 🔄 : mémoire d'échange pour soulager la RAM. (2GO Recommandé, = a la RAM)



## **🔥 Lancement de l'installation**

Quand tu démarres ton média d'installation (clé USB, CD...), tu choisis :

- **Graphical Install** 🖥️ (mode graphique)
- **Install** 🖋️ (mode texte)



## **🎛️ Paramétrages essentiels pendant l'installation**

### **🌍 1. Choix de la langue et du clavier**

- **Langue du système** : Français 🇫🇷 pour ce cours (Même si on trouve de l'anglais en prod, pour coller aux infos sur le net).
- **Disposition clavier** : Français également (pratique pour le dépannage).

### **🖥️ 2. Nom de machine**

- Donne un **hostname** 🏷️ à ton serveur, par exemple SRV-DEV01.

### **🌐 3. Domaine**

- Domaine **DNS** uniquement (⚠️ pas un domaine Windows) > Optionnel : tu peux laisser vide et configurer plus tard 🕓.



## **🔐 Gestion des utilisateurs et des droits**

### **👤 Mot de passe root**

- L'installateur te demande un **mot de passe root**. (Attention a la dispo du clavier lors de l'install)

### **⚖️ Deux écoles d'administration**

- **Avec mot de passe root** : Seul root 👑 peut tout faire. > Le 1er utilisateur ➔ simple utilisateur.
- **Sans mot de passe root** : Le 1er utilisateur devient admin via **sudo** ⚡.

💡 Pour accéder a root : su - ➔ PW root (⚠️ *le tiret est ultra important*). OU sudo -i / sudo -s ➔ si pas de mot de passe root (PW utilisateur).



## **🗂️ Partitionnement des disques**

- **LVM** 📦 est conseillé ➔ permet de mieux gérer l'espace disque. Attention, il faudra préparer une table de partitionnement.



## **📦 Installation des logiciels**
Tu choisis un **environnement de bureau** pour un PC. Ou tu fais une install **sans interface graphique** pour un serveur.



## **✅ Validation finale**

- **IMPORTANT** : installe bien **le chargeur d'amorçage** GRUB sur /dev/sda 💽. 🚨 Sinon, ton système sera là... mais ne démarrera jamais !

