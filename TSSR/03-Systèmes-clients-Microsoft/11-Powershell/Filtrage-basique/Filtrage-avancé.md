# Filtrage avancé**🎯 Filtrage avancé en PowerShell**

💡 **Objectif** : Affiner les résultats en filtrant sur **plusieurs propriétés** en même temps.



**🛠️ Utilisation du filtrage avancé**

🔹 **Commande de base** : ***Get-NetAdapter | Select-Object Name, Status, LinkSpeed***

➡️ Affiche **toutes** les interfaces réseau avec leurs propriétés **Name, Status, LinkSpeed**.



**🔍 Filtrer sur plusieurs critères**

💡 **On utilise Where-Object avec -Filterscript {} et la variable $_**

***Get-NetAdapter | Where-Object -Filterscript { $_.Status -eq "Up" -and $_.LinkSpeed -gt 100Mbps }***

➡️ **Affiche uniquement** les cartes réseau :

✔️ **Actives (Status -eq "Up")**

✔️ **Avec un débit supérieur à 100 Mbps (LinkSpeed -gt 100Mbps)**

📌 **Explication** :

- $_ représente **chaque objet retourné** par Get-NetAdapter
- Status -eq "Up" → Vérifie si l'interface est active
- LinkSpeed -gt 100Mbps → Vérifie si la vitesse est > 100 Mbps
- -and → Combine les conditions



⚠ **-Filterscript** peut-être facultatif si le Where-Object n'a qu'un paramètre, sinon il est obligatoire.



**⚡ Opérateurs utiles en filtrage avancé**

| **Opérateur** | **Utilisation** | **Exemple** |
|----|----|----|
| -and | ET logique | $_Status -eq "Up" -and $_LinkSpeed -gt 100Mbps |
| -or | OU logique | $_Status -eq "Up" -or $_Name -like "*Wi-Fi*" |
