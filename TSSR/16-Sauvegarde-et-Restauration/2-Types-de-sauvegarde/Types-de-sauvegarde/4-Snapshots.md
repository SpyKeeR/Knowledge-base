# Snapshots

🧠 **Définition**

Le **cliché instantané** (ou *snapshot*) est une **capture figée d’un volume à un instant T**, réalisée via le **Volume Shadow Copy Service (VSS)** de Microsoft.

🧩 Il permet de **sauvegarder un état complet** d’un volume NTFS, **même si des fichiers sont en cours d’utilisation**.

🔧 Basé sur :

- **VSS (Volume Shadow Copy Service)**
- Fonctionne sur les volumes **NTFS**
- Garantit une **cohérence des données**, même en cas de modifications pendant la prise de cliché



### ✅ **Avantages**

🔄 **Restauration rapide et granulaire**

- Permet de restaurer **un fichier individuel** ou **l’intégralité d’un volume**
- Pas besoin d'interrompre les services pour restaurer
- 💡 Idéal pour récupérer une **version précédente d’un document** perdu ou modifié par erreur

📁 **Sauvegarde cohérente sans interruption**

- Prise en charge des **fichiers ouverts ou en cours d'utilisation**
- Maintien de la **continuité des services** pendant la capture
- 🔐 Sauvegarde fiable et automatisable



### ⚠️ **Inconvénients**

💽 **Consommation d’espace disque**

- Chaque snapshot occupe **de l’espace sur le volume**
- ❗ Un trop grand nombre de clichés peut **remplir le disque**
- Requiert une **gestion rigoureuse** (limitation du nombre de clichés, rotation, nettoyage)

🐢 **Impact sur les performances**

- La présence de **multiples clichés** peut provoquer :  
  • Un ralentissement de l’accès disque  
  • Une **légère baisse des performances** du serveur, surtout si mal dimensionné
