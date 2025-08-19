# 3 - Gestion des paquets📦 **.deb vs .rpm — 2 grands mondes Linux**
- .deb → Debian, Ubuntu, Mint / .rpm → Red Hat, CentOS, Fedora, AlmaLinux : Les deux contiennent : **binaire compilé + métadonnées + fichiers de config**
- Format = archive structurée → pas juste un exécutable !



🧩 **dpkg / apt vs rpm / yum / dnf**

- **dpkg** (Debian) & **rpm** (RedHat) = **gestionnaires bas niveau** : installent localement, **pas de gestion des dépendances**
- **apt** (Debian) & **yum/dnf** (RedHat) = **gestionnaires haut niveau** : ajoutent la **résolution automatique des dépendances**, **recherches** dans les dépôts, **mise à jour**, **vérif d'intégrité** (dnf = successeur de yum, plus moderne, mais même logique)



🏷️ **RHEL vs CentOS**

- **RHEL** (Red Hat Enterprise Linux) = version **payante**, **support pro**, **certifications**, MAJ garanties
- **CentOS** (ancien) / **AlmaLinux**, **Rocky Linux** (aujourd’hui) = version **communautaire**, gratuite, **sans support officiel**, mais **code source identique**



🔧 **Fonctions d’un gestionnaire de paquets avancé**

- ✔️ **Résolution des dépendances**
- ✔️ **Vérification de l’intégrité des paquets**
- ✔️ **Authentification via signature GPG**
- ✔️ **Facilité de mise à jour**
- ✔️ **Groupement logique de paquets (meta-paquets)**
- ✔️ **Détection de conflits et rollback possible**



📀 **Commandes RPM**

- Installer ➜ **rpm -ivh telnet.rpm**
- Supprimer ➜ **rpm -e telnet**
- Mettre à jour ➜ **rpm -Uvh telnet.rpm**
- Interroger ➜ **rpm -q telnet**
- Vérifier ➜ **rpm -Vf /chemin/fichier**



🌐 **YUM – Gestionnaire haut niveau (basé RPM)**

- Lit les dépôts dans /etc/yum.repos.d/*.repo (Accès via FTP, HTTP, HTTPS)  
  ➤ **yum install httpd** (installation)  
  ➤ **yum remove httpd** (suppression)  
  ➤ **yum repolist** (liste des dépôts)  
  ➤ **yum provides scp** (trouver le paquet contenant une commande)  
  ➤ **yum update** ou **yum update telnet** (maj globale ou ciblée)

🛠️ **dpkg – Gestionnaire bas niveau (.deb)**

- Installer ➜ **dpkg -i telnet.deb**
- Supprimer ➜ **dpkg -r telnet**
- Lister ➜ **dpkg -l telnet**
- Statut ➜ **dpkg -s telnet**
- Vérifier ➜ **dpkg -p /chemin/fichier**



📡 **APT – Interface haut niveau pour .deb**

- Fichier des dépôts : /etc/apt/sources.list
- Commandes clés :  
  ➤ **apt update** (maj des listes)  
  ➤ **apt upgrade** (maj des paquets)  
  ➤ **apt install telnet** / **apt remove telnet**  
  ➤ **apt search telnet** (recherche)  
  ➤ **apt list | grep telnet** (filtrage manuel)  
  ➤ **apt edit-sources** (éditer les dépôts)
