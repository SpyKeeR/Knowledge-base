# Déploiement**💾 Stockage et Déploiement de l'Image**

- **Image capturée** : Après avoir capturé l'image du système, elle doit être **stockée** et prête à être **déployée** selon les besoins.



**🖥️ Serveur de Déploiement**

- Utilisez un **serveur de déploiement** pour installer l'image sur **plusieurs postes clients** (100, 200, 500 postes, etc.).
  - **Avantage** : Aucune intervention manuelle pour chaque installation. Le déploiement se fait **automatiquement**.



**🌐 Méthode de Déploiement**

- Les ordinateurs cibles doivent **démarrer sur le réseau(PXE)** et se **connecter au serveur(WDS).**
- Ils choisiront ensuite l'image de référence à déployer.



**🔄 Déploiement au Cas par Cas**

- **Support amorçable** : Utilisez des supports **WinPE** (clé USB, DVD) pour effectuer des déploiements **individuels** avec la commande **DISM**.



**🛠️ Préparation du Volume d'Accueil**

- Avant de déployer l'image, créez le **volume d'accueil** pour accueillir le contenu de l'image.
