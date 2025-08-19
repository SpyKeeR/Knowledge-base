# Diagnostic🔍 **Importance du diagnostic**

- Le processus d’installation via MDT est **complet et complexe**
- De nombreuses **problématiques** peuvent survenir, notamment lors de la **mise en œuvre initiale**
- Objectif : développer une **méthodologie d’analyse** pour diagnostiquer les défaillances efficacement

📄 **Analyse des journaux (logs)**

- Les **journaux de déploiement** sont essentiels pour identifier les causes d’échec
- Ils permettent d’examiner les étapes effectuées durant le déploiement
- Les logs peuvent être très **verbeux** : repérer les lignes pertinentes est capital

🛠️ **Méthodes d’analyse recommandées** :

- Accéder aux fichiers journaux via **invite de commande**
- Rechercher les erreurs dans les fichiers de logs pour identifier l’étape bloquante



🧭 **Accès aux journaux**

- 💬 En cas d’échec : une **invite de commande** peut s’ouvrir automatiquement
- ⌨️ Si absente : utiliser **Shift + F10** pour l’ouvrir manuellement
- 💽 Si aucune console disponible :
  - Démarrer sur le média d’installation
  - Aller dans **Dépannage > Invite de commande**

📁 **Exemple de navigation** : cd X:\MSDeployment\Logs  
Get-WinEvent -LogName "Microsoft-Windows-Deployment" | Export-Csv -Path "C:\Logs\MDT_Logs.csv" -NoTypeInformation

📌 **Emplacements courants des fichiers journaux**

🗂️ *Logs MDT spécifiques* :

- X:\MININT\SMSOSD\OSDLOGS\BDD.log
- C:\MININT\SMSOSD\OSDLOGS\BDD.log

🗂️ *Logs Windows (installation)* :

- C:\Windows\Panther\setuperr.log
- C:\Windows\Panther\setupact.log



📊 **Objectif final**

- Identifier **l’étape problématique** dans le processus
- Adopter une approche **systématique et rigoureuse** pour chaque analyse
- Renforcer sa capacité à dépanner les erreurs de déploiement efficacement ✅
