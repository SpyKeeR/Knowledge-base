# Get-Content**📘 Présentation de la commande Get-Content**

La commande **Get-Content** permet de **lire le contenu d’un fichier** directement dans la console PowerShell.

Elle affiche les lignes **en brut**, sans mise en forme, exactement comme elles sont stockées.

🔹 Utile pour : CSV, CLI XML, TXT

🔸 ⚠️ JSON ou HTML nécessitent un **traitement ou parsing préalable**



**📂 Lecture brute et comportement**

- Le contenu s’affiche **ligne par ligne**
- L’affichage peut être tronqué selon la **taille de la console**
- Pas d’interprétation : juste du texte pur

Exemple typique :

Get-Content C:\Users.csv

→ toutes les lignes du fichier s'affichent directement



**🔍 Paramètre -Tail : afficher les dernières lignes**

Le switch -Tail permet de **n’afficher que les dernières lignes** du fichier :

- -Tail 1 → 1 ligne (la dernière)
- -Tail 10 → 10 dernières lignes

Exemple courant :

Get-Content -Path "[\serveur\partage\Users.csv](file://serveur/partage/Users.csv)" -Tail 10

🧠 Très pratique pour inspecter rapidement les **ajouts récents** dans un gros fichier (log, export, etc.)



**🛠 Résumé des cas d’usage**

- **Lire du texte** rapidement sans ouvrir de logiciel
- **Inspecter du CSV/XML brut**
- **Extraire les dernières lignes** (logs/errors récents)
- **Utilisation réseau ou locale** fluide

⚠️ Ne pas confondre avec Import-Csv qui structure les données en objets ; ici on reste sur **du texte brut ligne à ligne**
