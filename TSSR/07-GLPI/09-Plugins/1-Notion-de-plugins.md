# 🧩 Les plugins dans GLPI

## **Qu’est-ce qu’un plugin ?**
- Un **plugin (ou extension)** permet **d'étendre les fonctionnalités natives** de GLPI sans modifier son cœur.  
- Ils peuvent être développés par la communauté ou par l’équipe GLPI.  
- Les plugins sont **autonomes** du noyau, et se gèrent séparément.  

🌐 **Catalogue officiel :** <https://plugins.glpi-project.org>  


On y retrouve :  
- 🔎 Les **fonctionnalités proposées**  
- 🔄 La **compatibilité** selon la version GLPI  
- 📊 Un classement par catégories :
   - Reporting,
   - Inventaire (ex : FusionInventory),
   - Réseau,
   - Gestion financière/administrative,
   - Import/Export (ex : Data-Injection)  


## **📍 Où ça se passe dans GLPI ?**  
↳ **Accueil > Configuration > Plugins**

C’est ici que tu installes, actives, configures ou désinstalles les plugins.  
👉 Les plugins permettent d’ajouter plein de fonctions (inventaire auto, cartographie réseau, gestion des contrats, etc.).



## **🛒 Installation depuis le Marketplace (méthode recommandée)**
1. Ouvre l’onglet **Marketplace**  
2. Deux sections :  
   - 🧩 **Installé** : plugins déjà présents  
   - 🔍 **Découvrir** : plugins compatibles avec ta version  
3. Clique sur un plugin compatible → **Installer**  
4. Puis **Activer** et éventuellement **Configurer**  

🧠 À retenir :  
- Les plugins **non compatibles** ne sont pas installables.  
- Certains nécessitent une **offre GLPI-Network** (support payant).  
- Les fichiers téléchargés sont stockés dans `/var/www/glpi/marketplace`.
- **Nécessite** :
   - 💻 Un **accès Internet** depuis le serveur GLPI.
   - 🔑 Une **clé API Registration GLPI Network** (à récupérer sur le site officiel après inscription).
      - Cette clé se configure dans : Configuration > Général > Clé d’authentification Marketplace


## **📁 Installation manuelle (méthode alternative)**
À utiliser uniquement si le plugin n’existe pas dans le Marketplace ou si tu as une version spécifique.

1. Télécharge l’archive depuis le site officiel  
2. Décompresse-la **sans renommer le dossier**  
3. Vérifie les droits (souvent `www-data`)  
4. Place le dossier dans `/var/www/glpi/plugins`  

👉 Ensuite, dans GLPI :  
- Le plugin apparaît dans **Installé**  
- Tu peux alors **Installer** puis **Activer**  

🧹 Pour désinstaller complètement : supprimer manuellement le dossier du plugin.  


## **🛠️ Gestion des plugins installés**
Depuis **Configuration > Plugins > Installé** :  
- Activer / Désactiver  
- Configurer ⚙️  
- Désinstaller (⚠️ des fichiers peuvent rester → nettoyage manuel possible)  


## **⚠️ Compatibilité et mises à jour**
Lors d’une mise à jour de GLPI :  
- Vérifie la **compatibilité des plugins principaux** avec la nouvelle version  
- Mets à jour **GLPI et tes plugins en parallèle**  
- Sinon, certaines fonctionnalités peuvent casser ou devenir inutilisables


