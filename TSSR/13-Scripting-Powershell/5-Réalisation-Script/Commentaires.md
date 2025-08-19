# Commentaires🔹 **Rôle des commentaires dans un script**

Les commentaires permettent de :

• Documenter l’intention ou le fonctionnement d’un bloc de code

• Tester du code sans l’exécuter (désactivation temporaire)

• Conserver des commandes alternatives sans les supprimer

• Faciliter la relecture et la reprise du script par d’autres personnes

• Suivre l’évolution d’un script au fil du temps

Un script bien commenté est un script maintenable et évolutif.



✏️ **Commentaire sur une seule ligne**

Le symbole # placé en début de ligne rend toute la ligne non exécutable.

Exemple : ligne désactivée ou note explicative avant une commande.

• # Cette commande est désactivée → Ligne ignorée

• Get-Process # Affiche les processus en cours → Seule la commande est exécutée, la suite est un commentaire



📋 **Commentaire sur plusieurs lignes (bloc)**

Pour commenter un bloc complet sans ajouter # à chaque ligne :

• Début du bloc : #<

• Fin du bloc : >#

Tout le contenu entre les deux balises est ignoré à l’exécution.

Permet de désactiver rapidement une structure entière (ex : if, boucle, etc.).



🛠️ **Utilisation typique dans un script**

• Tester plusieurs alternatives sans supprimer

• Désactiver temporairement une portion de code en cours de correction

• Documenter des blocs complexes étape par étape

• Faciliter la maintenance et les retours en arrière



📌 **À retenir**

• # pour un commentaire simple (ligne ou fin de ligne)

• #< … ># pour un commentaire bloc (plusieurs lignes)

• Ne jamais négliger les commentaires dans les scripts en production

• Plus un script évolue, plus les commentaires deviennent essentiels à sa compréhension et sa maintenance
