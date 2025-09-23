# Monitoring

### 👀 **Rôle du monitoring**

- Permet de **suivre en temps réel** les déploiements depuis la console MDT
- Utile surtout quand l’accès direct aux machines est difficile (pas d’écran branché, unités centrales éloignées)



## ⚙️ **Activation et configuration**

- 🔧 Se configure dans les **propriétés du partage de déploiement**, onglet **Monitoring**
- ⚠️ Désactivé par défaut, il faut l’activer manuellement
- 📝 Ajouter dans le fichier **CustomSettings.ini** la ligne : 
```ini
eventService=http://<nom_du_serveur_MDT>:9800
```
- 🔌 Le port **9800** doit être celui configuré dans MDT pour le service de monitoring



## 📈 **Suivi des déploiements**

- Via le menu **Monitoring** dans la console MDT, à gauche
- Visualisation de l’état d’avancement des déploiements des postes
- Indication claire quand le déploiement est **"completed"** (terminé avec succès)
- Affiche :
  - Temps total du déploiement
  - Nombre de tâches réalisées dans la séquence



## 🔑 **Points clés**

- Le monitoring offre une **visibilité précise** sur l’état des déploiements
- Essentiel pour les environnements où les machines ne sont pas facilement accessibles physiquement
- Permet une **meilleure gestion et contrôle** des opérations de déploiement


# Diagnostic

### 🔍 **Importance du diagnostic**

- Le processus d’installation via MDT est **complet et complexe**
- De nombreuses **problématiques** peuvent survenir, notamment lors de la **mise en œuvre initiale**
- Objectif : développer une **méthodologie d’analyse** pour diagnostiquer les défaillances efficacement

## 📄 **Analyse des journaux (logs)**

- Les **journaux de déploiement** sont essentiels pour identifier les causes d’échec
- Ils permettent d’examiner les étapes effectuées durant le déploiement
- Les logs peuvent être très **verbeux** : repérer les lignes pertinentes est capital

### 🛠️ **Méthodes d’analyse recommandées** :

- Accéder aux fichiers journaux via **invite de commande**
- Rechercher les erreurs dans les fichiers de logs pour identifier l’étape bloquante



## 🧭 **Accès aux journaux**

- 💬 En cas d’échec : une **invite de commande** peut s’ouvrir automatiquement
- ⌨️ Si absente : utiliser **Shift + F10** pour l’ouvrir manuellement
- 💽 Si aucune console disponible :
  - Démarrer sur le média d’installation
  - Aller dans **Dépannage > Invite de commande**

### 📁 **Exemple de navigation** : 
```powershell
cd X:\MSDeployment\Logs  
Get-WinEvent -LogName "Microsoft-Windows-Deployment" | Export-Csv -Path "C:\Logs\MDT_Logs.csv" -NoTypeInformation
```

📌 **Emplacements courants des fichiers journaux**

### 🗂️ *Logs MDT spécifiques* :
```dos
X:\MININT\SMSOSD\OSDLOGS\Deploy.log
X:\MININT\SMSOSD\OSDLOGS\SMSTS.log
X:\MININT\SMSOSD\OSDLOGS\BDD.log
C:\MININT\SMSOSD\OSDLOGS\BDD.log
C:\MININT\SMSOSD\OSDLOGS\SMSTS.log
C:\MININT\SMSOSD\OSDLOGS\Deploy.log
C:\Windows\Temp\DeploymentLogs\BDD.log
C:\Windows\Temp\DeploymentLogs\SMSTS.log
C:\Windows\Temp\DeploymentLogs\Deploy.log
```

### 🗂️ *Logs Windows (installation)* :
```dos
C:\Windows\Panther\UnattendGC\setupact.log
C:\Windows\Panther\UnattendGC\setuperr.log
C:\Windows\Panther\setupapi.dev.log
C:\Windows\Panther\setupapi.app.log
C:\Windows\Panther\setupapi.offline.log
C:\Windows\Panther\setupapi.log
C:\Windows\Panther\miglog.xml
C:\Windows\Panther\setuperr.log
C:\Windows\Panther\setupact.log
```


## 📊 **Objectif final**

- Identifier **l’étape problématique** dans le processus
- Adopter une approche **systématique et rigoureuse** pour chaque analyse
- Renforcer sa capacité à dépanner les erreurs de déploiement efficacement ✅
