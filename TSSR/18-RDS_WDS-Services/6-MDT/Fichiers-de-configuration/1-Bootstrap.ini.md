## 🔧 **Rôle et importance du fichier bootstrap.ini**

- 📥 Contient les **informations essentielles** pour le démarrage via **Windows PE**
- 🔗 Permet l’**accès au partage de déploiement** (contenant l’image install.wim)
- 🌐 Configure les **paramètres régionaux** (ex. : langue du clavier)
- 🔒 Peut inclure les **identifiants d’accès** au partage

 

## 🧭 **Contenu typique d’un bootstrap.ini**
```ini
[Settings]  
Priority=Default

[Default]  
DeployRoot=\\WDS\DeploymentShare  
UserID=mdt  
UserDomain=deploy.eni  
UserPassword=Pa$$w0rd  
KeyboardLocale=fr-FR  
KeyboardLocalePE=040c:0000040c  
SkipBDDWelcome=YES
```

### 📝 Explications :

- **DeployRoot** : chemin réseau vers le partage MDT
- **UserID, UserDomain, UserPassword** : authentification automatique
- **KeyboardLocale, KeyboardLocalePE** : disposition clavier pour l’utilisateur et dans WinPE
- **SkipBDDWelcome** : saute l’écran d’accueil BDD (Begin Deployment)

 

## 🔁 **Prise en compte des modifications**

✅ Toute modification du fichier bootstrap.ini nécessite :

1.  📂 **Mise à jour du partage de déploiement** dans MDT  
    - 👉 Clic droit sur **Deployment Share** > **Update Deployment Share**
    - 🔄 Cela régénère l’image `boot.wim` (aussi appelée `lite-touch.wim`)

2.  🖥️ **Mise à jour dans WDS**
    - 🔁 Remplacer l’**image de démarrage existante** par le nouveau `boot.wim`

