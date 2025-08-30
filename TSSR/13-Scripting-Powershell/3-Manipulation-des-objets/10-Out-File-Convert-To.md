# 📁 **🔄 Redirection vers fichier avec Out-File**

La cmdlet Out-File redirige la sortie (via pipe |) d'une commande vers un fichier texte, comme le ferait > ou >> en Bash.

Exemple : `Get-Service | Out-File "service.txt"`

## 🛠️ **⚙️ Paramètres essentiels de Out-File**

• `-Append` ➜ ajoute au contenu existant (sinon, écrasement)

• `-Width [valeur]` ➜ limite la largeur de ligne (défaut : 80 caractères)

📌 Le fichier cible doit être accessible et autorisé en écriture.

### 🧪 **💡 Exemple simple**

Lister les services et exporter dans un fichier :

• Sans remplacement ➜ `Get-Service | Out-File "C:\ENI\Services.txt"`

• En ajout ➜ ... `-Append`

• Avec largeur ➜ ... `-Width 42`



## 🔄 **📐 Export avancé : ConvertTo-* + Out-File**

Pour des formats non natifs (HTML, JSON), on convertit d’abord avec `ConvertTo-Html` ou `ConvertTo-Json`, puis on utilise `Out-File` pour l’export.



### 🌐 **🧾 Export HTML**

Permet une vue lisible dans un navigateur :

`Get-ADUser -Filter * | ConvertTo-Html | Out-File "Utilisateurs.html"`

📎 Résultat lisible directement via navigateur web. Idéal pour présentation ou intégration web.

### 🔧 **🧱 Export JSON**

Structure adaptée aux API et apps modernes :

`Get-ADComputer -Filter * | ConvertTo-Json | Out-File "Ordinateurs.json"`

📎 Vérification de la structure via Notepad++ (lecture/validation JSON).
