# Fichiers de config.🧭 **Présentation générale**

MDT repose sur deux fichiers de configuration principaux pour automatiser et personnaliser les déploiements :

- **Bootstrap.ini  **
  🔁 Chargé au démarrage de WinPE  
  🧭 Configure la connexion initiale au partage de déploiement

- **CustomSettings.ini  **
  ⚙️ Lu pendant la phase de déploiement  
  📋 Définit les paramètres, règles et actions à appliquer (langue, nom machine, applications…)



📂 **Accès et modification des fichiers**

📁 Emplacement :

📌 Répertoire Control du partage de déploiement MDT

🛠️ Méthode recommandée :

✔️ Utilisation de la **console MDT** pour garantir une intégration cohérente

📝 Édition depuis MDT :

- **CustomSettings.ini  **
  👉 Onglet **Rules** dans les propriétés du partage de déploiement

- **Bootstrap.ini  **
  👉 Bouton **Edit Bootstrap.ini** en bas à droite de l’onglet Rules  
  🔔 Ne pas oublier de sauvegarder les modifications

🧾 **Exemple de configuration minimale**

[Settings]  
Priority=Default

[Default]  
OSInstall=Y

- Ce type de configuration permet à MDT de démarrer automatiquement le processus d’installation de l’OS sans interaction

📌 **Récapitulatif**

✅ **Bootstrap.ini** → Configuration au boot de WinPE

✅ **CustomSettings.ini** → Définition des règles pendant l’installation

🔧 Les deux fichiers permettent un déploiement automatisé, contrôlé et adaptable aux besoins spécifiques
