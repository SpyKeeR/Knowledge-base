# Déploiement O365**🖥️ Versions et déclinaisons du Pack Office**

- Office ProPlus : Word, Excel, PowerPoint, OneNote, Publisher, Access, SharePoint Designer
- Applications pour Mac, iPad, iPhone, Android
- Versions locales et en ligne (Office Online)



**⚙️ Fonctionnalités clés d’Office 365**

- Co-authoring (édition simultanée)
- Intégration OneDrive & Teams
- Support multiplateforme
- Priorisation des mails dans Outlook
- Mises à jour régulières (fonctionnalités + sécurité)

**🔄 Canaux de mise à jour**

- Canal mensuel : mises à jour fréquentes
- Canal semi-annuel : mises à jour tous les 6 mois (janvier, juillet)
- Canal semi-annuel ciblé : utilisateurs pilotes pour tests (mars, septembre)



**👥 Gestion des installations par les utilisateurs**

- Installation via portail Office 365 ("Installer Office")
- Règle 3 x 5 : installation possible sur 3 appareils max × 5 postes utilisateurs



**💾 Installation avec sources locales**

- Problème de bande passante évité grâce au téléchargement local
- Création d’un partage réseau (ex : \Serveur\Office_365)
- Extraction de l’Office Deployment Tool (ODT.exe) dans ce répertoire
- Commandes principales :
  - setup.exe /download : télécharger les fichiers
  - setup.exe /configure : lancer l’installation selon un fichier XML de configuration personnalisé



**🔧 Création du fichier de configuration XML**

- Utilisation du site officiel : <https://config.office.com/>
- Choix : architecture (32/64 bits), logiciels à déployer, canal de mise à jour, langues, options supplémentaires
- Possibilité d’intégrer des options de mise à niveau (désinstaller anciennes versions)
- Activation automatique des licences

**🗂️ Déploiement via GPO (Stratégie de Groupe)**

- Créer un partage nommé (ex : Office_365) sur contrôleur de domaine
- Extraire officedeploymenttool.exe dans ce partage
- Placer le fichier de configuration XML dans ce répertoire
- Créer une OU contenant les machines cibles
- Créer et configurer une GPO liée à cette OU :
  - Ajouter un script de démarrage (.cmd) qui lance setup.exe /configure
- Sur les postes clients, l’utilisateur doit être administrateur local
- Redémarrer la machine pour déclencher le déploiement



**📑 Récapitulatif des étapes**

1.  Préparer le partage réseau avec ODT et fichiers XML
2.  Télécharger les fichiers d’installation via setup.exe /download
3.  Créer OU et GPO ciblant les machines
4.  Ajouter script de déploiement dans la GPO
5.  Redémarrer les postes clients pour installation automatique
