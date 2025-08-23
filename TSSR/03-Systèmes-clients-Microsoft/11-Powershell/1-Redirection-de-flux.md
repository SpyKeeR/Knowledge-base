# **🎯 Redirection des flux en PowerShell**

## 💡 **Les flux standards :**

🔹 **STDIN (Entrée standard – 0)** : Par défaut, provient du **clavier** ⌨️

🔹 **STDOUT (Sortie standard – 1)** : Par défaut, s’affiche à **l’écran** 🖥️

🔹 **STDERR (Sortie d’erreur – 2)** : Par défaut, les erreurs sont affichées **à l’écran** ⚠️



### 📌 **Pourquoi rediriger les flux ?**

✅ **Stocker les résultats** dans un fichier pour analyse 📂

✅ **Créer des logs** pour suivre l’exécution des scripts 📜

✅ **Ignorer les erreurs** pour éviter le bruit inutile 🚫



## **🛠️ Redirection en pratique**

### 🔹 **Rediriger la sortie standard vers un fichier** : 
- `Get-Process > processus.txt`

➡️ Si processus.txt n’existe pas, il est créé. ➡️ S’il existe, son contenu est **écrasé** ❌



### 🔹 **Ajouter la sortie standard à un fichier (sans écraser)** : 
- `Get-Process >> processus.txt`

➡️ Ajoute les nouvelles données **à la fin** du fichier 📌



### 🔹 **Rediriger les erreurs dans un fichier** : 
- `Get-Process xyz 2> erreurs.txt`

➡️ Capture uniquement les erreurs et les stocke dans erreurs.txt 🚨



### 🔹 **Ajouter les erreurs à un fichier (sans écraser)** : 
- `Get-Process xyz 2>> erreurs.txt`

➡️ Ajoute les nouvelles erreurs **à la fin** du fichier 📌



### 🔹 **Capturer sortie + erreurs dans le même fichier** : 
- `Get-Process xyz > resultat.txt 2>&1`

➡️ Fusionne la sortie standard et les erreurs dans resultat.txt



### 🔹 **Ignorer complètement les erreurs** : 
- `Get-Process xyz 2> $null`

➡️ Envoie les erreurs vers $null (poubelle numérique 🗑️)

## ⚡ **Résumé rapide :**

| **Opérateur** | **Effet**                       |
|---------------|---------------------------------|
| >            | Écrit (écrase) dans un fichier  |
| >>          | Ajoute à un fichier             |
| 2>           | Capture les erreurs             |
| 2>>         | Ajoute les erreurs à un fichier |
| 2>&1         | Fusionne sortie et erreurs      |
| 2> $null    | Supprime les erreurs            |

