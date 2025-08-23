# Filtrage basique

💡 **Pourquoi filtrer ?**

Le pipeline transmet **tous les objets** générés par une commande. Pour **garder uniquement ce qui nous intéresse**, on utilise **le filtrage** !



## **🛠️ Méthodes de filtrage**

🔹 **1️⃣️ Sélectionner des propriétés** (via **Select-Object**) 
- `Get-NetAdapter | Select-Object Name, Status, LinkSpeed`

➡️ **Affiche uniquement** les colonnes Name, Status et LinkSpeed

🔹 **2️⃣ Filtrer selon une valeur** (via **Where-Object**)
- `Get-NetAdapter | Where-Object Status -like "Up"`

➡️ **Affiche seulement** les cartes réseau **actives** (Status = Up)



## **🤔 Pourquoi utiliser $_. ? Et quand ne pas l’utiliser ?**

### 🛠 **Exemple avancé :** Filtrer les cartes réseau actives contenant "Wi-Fi"
- `Get-NetAdapter | Where-Object { $_.Status -eq "Up" -and $_.Name -like "*Wi-Fi*" }`

🔹 **Cas où on utilise $_.**

- $_ représente **chaque élément traité dans le pipeline**.
- On utilise {} pour définir un **bloc de script** (scriptblock).
- $_ représente **chaque élément du pipeline**, utile pour appliquer une **condition complexe**.
- Obligatoire quand on veut **combiner plusieurs conditions**.

### **📌 Version simplifiée : Where-Object Propriété -Opérateur Valeur**
- `Get-NetAdapter | Where-Object Status -like "Up"`

✅ **Pourquoi ?**

- Cette forme est une **simplification récente** introduite à partir de **PowerShell 3.0**.
- Permet d’écrire plus court quand on applique un **test simple** sur une propriété.

⚠ **Limite :** Cette syntaxe ne fonctionne pas si on veut **plusieurs conditions**.

## **📌 Les opérateurs de comparaison**

| **Opérateur** | **Signification**             |
|---------------|-------------------------------|
| -eq           | Égal à                        |
| -ne           | Différent de                  |
| -gt           | Supérieur à                   |
| -ge           | Supérieur ou égal à           |
| -lt           | Inférieur à                   |
| -le           | Inférieur ou égal à           |
| -like         | Contient (* pour joker)      |
| -notlike      | Ne contient pas               |
| -match        | Correspondance regex          |
| -notmatch     | Ne correspond pas regex       |
| -contains     | Contient valeur dans tableau  |
| -notcontains  | Ne contient pas               |
| -in           | Est contenu dans...           |
| -notin        | N'est pas contenu dans…       |
| Prefixe -c    | -ceq/-clike > Sensible Casse |


