# Filtrage stdout / Grep et pipeDans ce chapitre, on apprend à **chercher efficacement** des infos dans des fichiers ou des sorties de commandes, grâce à des outils simples mais puissants, comme grep et le **pipe** |.

**📂 Faire des recherches précises**

**Objectif :** ne pas se noyer dans des tonnes d’infos, mais **extraire ce qui nous intéresse**.

**Exemple basique :** *ls | grep ".txt"*

→ Montre uniquement les fichiers .txt dans le dossier courant.



**🔗 Enchaînement de commandes avec | (pipe)**

Le **pipe** | permet de **passer la sortie** d’une commande **en entrée** d’une autre.

Très utilisé avec grep pour filtrer des résultats, mais fonctionne avec plein d'autres commandes.

**Exemple :** *cat notes.txt | grep "linux"*

→ Affiche uniquement les lignes contenant "linux" dans notes.txt.



**🧰 La commande grep**

**Utilité :** chercher une **chaîne de caractères** ou **motif** (pattern) dans un fichier ou une sortie de commande.

**Syntaxe :** *grep [options] "motif" fichier*

ou avec un pipe : *commande | grep "motif"*



**⚙️ Options utiles de grep**

- -e : recherche plusieurs motifs (un -e par motif)
- -i : ignore la casse (maj/min)
- -l : affiche juste les **noms de fichiers** où le motif est trouvé
- -n : affiche le **numéro des lignes** trouvées
- -v : inverse la recherche → affiche tout **sauf** les lignes contenant le motif



**🧹 Exemple de filtrage avec exclusion**

*ls | grep -v "T"* → Affiche tous les fichiers sauf ceux contenant un "T".



**🧮 Chaine de pipes**

On peut **enchaîner plusieurs pipes** !

**Exemple :** *ls | grep ".txt" | wc -l* → Compte le nombre de fichiers .txt.
