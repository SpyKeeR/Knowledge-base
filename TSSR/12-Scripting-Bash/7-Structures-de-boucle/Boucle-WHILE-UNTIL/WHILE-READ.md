# WHILE/READTraiter un **fichier ligne par ligne** (ou un flux de commande) et découper chaque ligne en **champs distincts**. Contrairement à for, cette méthode **respecte les lignes entières**, sans casser à chaque espace ou retour à la ligne.

**🧠 Pourquoi préférer while read à for ?**

- for découpe les mots selon l’IFS (espace, tab, \n) → **perte du format ligne**
- while read lit **chaque ligne complète** dans l'ordre → **plus fiable** pour lire des fichiers structurés (CSV, TSV, etc.)



**🔧 Structure**

➡️ while read champ1 champ2 reste ; do ACTIONS ; done < fichier.txt

- champ1, champ2 → noms choisis pour les premiers champs
- reste → variable fourre-tout pour ignorer ce qui suit  
  💡 Permet de **ne pas écraser** les champs en cas d’espace dans les prénoms ou noms composés



**🧪 Exemple**

*Contenu de fichier.txt*

thouin Frédéric linux  
brossier Gilles windows



*Boucle*

while read nom prenom suite ; do echo "$prenom $nom" ; done < fichier.txt



*Résultat*

Frédéric thouin  
Gilles brossier

**🌀 Alternatives pour rediriger l’entrée**

Tu peux aussi utiliser une **commande en substitution de process** (plus lisible) :

➡️ while read ... ; do ... ; done < <(commande)

ou un **pipe** : commande | while read ... ; do ... ; done

🧠 Attention : le pipe crée un **sous-shell**, donc les variables internes à la boucle sont **perdues en dehors**.



**⚠️ Cas particulier : read interactif dans la boucle**

Exemple : tu veux faire une saisie utilisateur pendant un traitement sur fichier avec un pipe (ou un < <(...)).

Le read classique va **planter** car le stdin est occupé par le fichier.

🩹 **Solution** : rediriger vers /dev/tty

➡️ read -p "message : " choix </dev/tty

Ça permet de **lire au clavier**, même si le script lit déjà depuis un fichier ou une commande.
