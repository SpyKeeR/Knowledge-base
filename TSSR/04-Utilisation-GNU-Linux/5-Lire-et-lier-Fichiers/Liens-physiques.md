# Liens physiquesContrairement aux liens symboliques, les **liens physiques** pointent directement vers l’**inode** du fichier, donc vers les **données elles-mêmes**. C’est une autre manière de "renommer" un fichier sans le dupliquer, en partageant le même identifiant système.



**🛠️ Création d’un lien physique**

*ln source cible*

📌 Pas d’option -s ici.

📌 Le **lien physique** est juste un **autre nom** pour le même fichier.

📌 Impossible de créer un lien physique vers un **répertoire** ou sur **une autre partition**.



**⚙️ Fonctionnement interne**

🔁 Tous les liens physiques partagent le **même inode**.

🧠 Le système voit plusieurs noms qui **pointent vers la même structure de données**.

📜 En listant avec ls -i, on voit le **même numéro d’inode** pour chaque lien.



**🔎 Affichage et comportement**

📄 Les fichiers apparaissent comme des **fichiers normaux**, sans coloration spéciale.

📈 Le nombre de liens est visible entre les droits et le propriétaire (ex: -rw-r--r-- 2 user...).

➕ Chaque nouveau lien physique **augmente ce compteur**,

➖ Chaque suppression **le décrémente**. Quand il atteint zéro, l’inode est libéré.



**🚚 Mobilité & robustesse**

🪄 Contrairement aux liens symboliques, les liens physiques **ne sont pas affectés** par le **déplacement** du fichier source.

📦 Tu peux déplacer ou renommer les fichiers liés, les **données restent accessibles** tant qu’au moins un lien existe.

⚠️ Impossible de lier entre **partitions/disques différents**, car chaque partition a sa propre table d’inodes.



**🧹 Suppression**

🗑️ Supprimer un lien physique ne détruit **pas** les données tant qu’il reste d'autres liens.

🔚 Quand le **dernier lien** est supprimé, l’inode est libéré → les **données sont effacées** du disque.



**✅ Avantages**

✔️ Pas sensible au **chemin** (contrairement aux liens symboliques)

✔️ Accès plus rapide, car lien direct vers l’inode

✔️ Pas de liens cassés si on déplace les fichier

**❌ Inconvénients**

❗ Ne fonctionne que sur le **même système de fichiers**

❗ Impossible de faire un lien vers un **répertoire**

❗ Pas de repérage visuel dans le terminal (pas de couleur spécifique)
