# Liens symboliques

Les liens symboliques sont l’équivalent des raccourcis sous Windows. Ultra pratiques, ils permettent de pointer vers un fichier ou un dossier sans le dupliquer. Pour les créer, on utilise toujours la commande **ln**, avec l'option -s pour les liens symboliques.



## **🛠️ Création d’un lien symbolique**

`ln -s source cible`

📌 Le **fichier source** est l’original.

📌 Le **lien (cible)** est le raccourci.

💡 On peut lier un fichier vers fichier, fichier vers dossier, ou dossier vers dossier.



## **🧠 Fonctionnement**

🧷 Le lien pointe vers le chemin du fichier, pas vers son contenu.

🔁 Toute modification faite via le lien est appliquée sur le fichier original.

📄 Le lien et le fichier source ont chacun leur **inode** (métadonnées séparées).



## **👁️ Visibilité & repérage**

👕 Avec la coloration syntaxique : les liens apparaissent **en cyan**.

📜 Avec `ls -l` : le type de fichier s’affiche comme un lien (l au début de la ligne).

🧩 Le lien contient juste le **chemin d’accès** au fichier source.



## **⚠️ Attention aux chemins !**

❗ La commande ln est **très sensible** aux chemins relatifs.

🚫 Si le chemin est mal indiqué → **lien cassé** (souvent affiché en rouge).

✅ Astuce : crée le lien depuis le dossier de la cible, ou utilise des **chemins absolus** pour éviter les erreurs.



## **🗑️ Suppression & impact**

🧹 Supprimer un **lien symbolique** ne touche pas le fichier source.

❌ Si le **fichier source** est supprimé ou déplacé → le lien devient **invalide** (lien mort).



## **✅ Avantages**

✔️ Peut lier à travers différentes **partitions**

✔️ Transparence totale pour le système

✔️ Fonctionne aussi pour des **répertoires**

✔️ Facile à repérer et à supprimer

## **❌ Inconvénients**

❗ Si le fichier original est **supprimé** ou **déplacé**, le lien ne suit pas.

❗ Le lien devient un raccourci mort, comme un 404 sur un site web.

