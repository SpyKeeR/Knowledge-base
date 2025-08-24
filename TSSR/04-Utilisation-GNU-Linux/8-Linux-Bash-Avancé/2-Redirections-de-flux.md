# Redirections de flux

## **🧠 Rappels sur les flux**

Chaque commande utilise 3 flux par défaut :

- **STDIN (0)** → Entrée standard (clavier)
- **STDOUT (1)** → Sortie standard (écran)
- **STDERR (2)** → Sortie d’erreurs



## **📬 Utiliser un pipe |**

Le **pipe** permet d’envoyer la sortie (stdout) d’une commande dans l’entrée (stdin) d’une autre.

`cat fichier.txt | grep "terme"`

## **📁 Rediriger la sortie dans un fichier**

- `commande > fichier.txt` → Écrase le fichier.
- `commande >> fichier.txt` → Ajoute à la fin du fichier.



## **🚨 Rediriger les erreurs**

- `commande 2> erreurs.log` → Redirige seulement les erreurs (stderr).



## **📑 Fusionner sortie + erreurs dans un fichier**

- `commande > fichier.log 2>&1`
- `commande &> fichier.log` (pas supporté partout)



## **📤 Afficher + enregistrer avec tee**

- `commande | tee fichier.txt` → Affiche à l’écran **et** écrit dans le fichier.
- `commande | tee -a fichier.txt` → Même chose, mais ajoute au fichier.



## **📥 Lire depuis un fichier (entrée)**

- `commande < fichier.txt` → Utilise le contenu du fichier comme **entrée** de la commande.

