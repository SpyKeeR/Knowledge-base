🧭 **1\. Où ça se passe ?** ↳ **Accueil > Configuration > Plugins**

C’est ici que tu installes, actives, configures, désinstalles et explores les **plugins** qui permettent de **booster** ton GLPI.

📦 Les plugins, c’est ce qui permet d’ajouter plein de fonctionnalités à GLPI sans toucher au cœur du système (comme l’inventaire automatique, la cartographie réseau, ou la gestion des contrats).

&nbsp;

🛒 **2\. Installation depuis le Marketplace (méthode recommandée)**

1. Clique sur **« Marketplace »**
2. Deux onglets :
    - 🧩 **Installé** : Plugins déjà présents sur ton système
    - 🔍 **Découvrir** : Plugins compatibles avec ta version de GLPI
3. Sélectionne un plugin compatible → Clique sur **Installer**
4. Une fois installé, tu dois **l’activer**
5. Ensuite, **Configure-le** si besoin

🧠 **À retenir :**

- Les plugins **non compatibles** ne seront **pas installables**
- Certains plugins nécessitent une **offre GLPI-Network** (GLPI payant avec support)

&nbsp;

🧰 **3\. Gestion des plugins installés**

Depuis l’onglet **Installé**, tu peux :

- Activer / désactiver un plugin
- Le configurer (⚙️)
- Le désinstaller

Si tu désinstalles un plugin, il peut rester des **fichiers résiduels** sur le disque.

🧹 Pense à faire le ménage dans le dossier /var/www/glpi/marketplace si besoin.

&nbsp;

📁 **4\. Installation manuelle (à faire uniquement si besoin)**

👉 Cas typique : le plugin n’est pas dispo sur le Marketplace ou tu as une version modifiée.

🔧 À faire sur le **serveur GLPI** :

1. Télécharge l’archive depuis le site du plugin
2. Décompresse-la (⚠️ **Ne renomme pas** le dossier !)
3. Assure-toi que les **droits appartiennent à www-data**
4. Déplace le dossier dans : /var/www/glpi/plugins

🖥️ Ensuite, sur l’interface GLPI : Le plugin apparaîtra dans l’onglet **Installé et** Tu pourras alors l’**installer**, puis l’**activer**

🧽 Pour désinstaller complètement : Supprime manuellement le dossier dans /var/www/glpi/plugins
