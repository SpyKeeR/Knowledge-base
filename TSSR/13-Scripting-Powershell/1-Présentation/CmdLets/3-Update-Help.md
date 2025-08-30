# Update-Help

PowerShell **n’installe pas automatiquement l’aide complète** (volontairement pour économiser de la bande passante à l’install).

`Update-Help` permet donc de :

- ✅ Télécharger les dernières versions de l’aide (offline par défaut)
- 🌍 Mettre à jour via Internet ou une source locale
- 🌐 Choisir la langue (par défaut : anglais)



### **⚙️ Fonctionnement global**

Lorsque tu exécutes `Update-Help` :

- PowerShell **scanne les modules installés**
- Il **tente de récupérer l’aide en ligne** depuis Microsoft
- Si ça échoue (pare-feu, proxy…), tu peux :
  - ➕ spécifier un **répertoire local** avec -SourcePath
  - 🗣️ forcer la langue avec -UICulture (ex : FR-FR, EN-US)



### **🛠️ Exemples d’utilisation**

- `Update-Help` ➜ mise à jour via Internet (mode classique)
- `Update-Help -SourcePath D:\PowerShell` ➜ mise à jour depuis un dossier local contenant les fichiers d’aide
- `Update-Help -SourcePath D:\PowerShell -UICulture fr-FR` ➜ idem mais version française

⚠️ Nécessite les droits admin pour fonctionner correctement (sauf cas spécifiques)



## **💾 À quoi sert Save-Help ?**

Elle te permet de **télécharger les fichiers d’aide** une bonne fois pour toutes depuis Internet, **les stocker dans un dossier**, et ensuite utiliser` Update-Help` avec -SourcePath sur d'autres machines.

👉 Utile pour :

- 📡 Machines sans accès Internet
- 🧪 Environnements de test isolés
- 👨‍🏫 Formations avec de nombreuses VMs à mettre à jour sans tout re-télécharger



### **🔗 Workflow typique**

Voici la logique en 3 étapes :

1.  🧲 **Télécharger une seule fois** l’aide : `Save-Help -DestinationPath D:\HelpOffline`
2.  💼 **Transférer ce dossier** sur les autres machines (clé USB, partage réseau…)
3.  🔄 **Mettre à jour l’aide localement** : `Update-Help -SourcePath D:\HelpOffline`

🗣️ Ajoute -UICulture fr-FR aux deux commandes pour la version française.

