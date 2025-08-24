# Lecture de fichier

## **🐱 cat – Lire ou créer un fichier**

Affiche tout le contenu d’un fichier en une seule fois.

📌 Lecture simple, rapide, mais peu pratique pour les gros fichiers.

📌 Permet de concaténer (cat fichier1 fichier2) ou de créer un fichier (cat > nouveau.txt + CTRL+D pour terminer).



## **📄 more – Lecture page par page**

Affiche un fichier texte page par page.

➡️ Espace pour avancer, q pour quitter.

- 🛠️ Option -d : message d’aide en bas. 
- 🛠️ Option -s : supprime les lignes blanches en double.

❗ Lecture uniquement vers le bas.



## **📖 less – Lecture avancée**

Une version améliorée de more avec navigation libre.

🔎 /mot pour chercher un texte, n et N pour naviguer entre les occurrences.

🔁 G pour aller à la fin, gg pour le début, j et k pour défiler ligne par ligne.

ℹ️ h pour afficher l’aide intégrée.

🛠️ Options utiles : 
- -s (supprime doublons)
- -i (ignore la casse).

✅ Ne quitte pas à la fin du fichier : tu peux remonter, faire d'autres recherches, etc.



## **🔼 head – Affiche le début**

Montre les premières lignes d’un fichier.

📌 Par défaut : 10 lignes. 

🛠️ -n 5 ou -5 (Linux uniquement) pour personnaliser.



## **🔽 tail – Affiche la fin**

Montre les dernières lignes.

📌 Par défaut : 10 lignes.

- 🛠️ -n pour choisir combien de ligne. 
- 🔥 -f pour suivre un fichier en temps réel (logs, etc.). CTRL+C pour sortir.



## **🔢 wc – Compter le contenu**

Compte ce que contient un fichier texte.

🛠️ -l lignes, -w mots, -m caractères, -c octets.

💡 Exemple : wc -l fichier.txt → te donne le nombre de lignes.


