# Gestion📌 **🔧 À quoi sert un pilote ?**

🔹 Le pilote (driver) est un **manuel d’utilisation** pour que l’OS puisse exploiter un périphérique

🔹 Windows 10 utilise des pilotes adaptés à l'**architecture** du matériel (x86, x64…)

🔹 **Pilotes signés** ✅ → Fournis avec un certificat pour éviter les écrans bleus liés à des drivers instables

📌 **📂 Structure d’un pilote**

🔹 .inf → Définition du pilote (texte)

🔹 .sys → Fichier compilé utilisé par le système

🔹 .cat → Signature numérique du pilote

🔹 **Installation** via clic droit sur .inf ou via un **setup.exe** du fabricant

📌 **📁 Où sont stockés les pilotes ?**

🔹 **Pilotes installés** :

- C:\Windows\INF → Fichiers .inf
- C:\Windows\System32\drivers → Fichiers .sys
- C:\Windows\System32\DriverStore → Magasin de pilotes

📌 **📦 Magasin de pilotes Windows**

🔹 Contient des **pilotes génériques préinstallés**

🔹 Permet une **détection automatique** des périphériques

🔹 **Limite** : Fonctionnalités parfois réduites comparées aux pilotes constructeurs

📌 **⚙️ Commandes utiles**

🔹 pnputil → Gérer les pilotes du magasin Windows (ajout, suppression)

🔹 driverquery → Lister les pilotes installés

🔹 msinfo32 → Voir les infos détaillées sur le système et les pilotes 🚀
