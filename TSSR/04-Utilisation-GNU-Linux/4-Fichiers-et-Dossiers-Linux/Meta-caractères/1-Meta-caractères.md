# Meta-caractères

Les **méta-caractères** permettent de faire des recherches ou manipulations de fichiers plus **flexibles**. C’est un outil **indispensable** dans l’univers Linux.



## **🌟 * — Le joker universel**

→ Remplace **n'importe quel nombre de caractères** (y compris zéro)

`ls *.txt` # tous les .txt  
`ls tel*` # tous les fichiers commençant par "tel"



## **❓ ? — Un seul caractère**

→ Remplace **un caractère unique >** ls tel20?? # tel2018, tel2021, etc.



## **🔢 [] — Liste ou plage de caractères**

→ Correspond **à un seul caractère parmi une liste/plage**
- `ls tel20[1-2][0-9]` # tel2010 → tel2029



## **🚫 \[!...] ou \[^...] — Négation**

→ Exclut certains caractères en **début de nom** 
`ls [!Tt]*` # fichiers ne commençant pas par T ou t



## **🗨️ Quotes : simple vs double**

- **Doubles quotes** " : interprètent les variables
  - `echo "Salut $USER"` # affiche le nom de l'utilisateur
- **Simples quotes** ' : affichent tel quel (pas d’interprétation)
  - `echo 'Salut $USER'` # affiche littéralement $USER



## **🔙 Antislash \\ — Échappement**

→ Utilisé pour **désactiver un méta-caractère**



## 🎯 **$()** - **Appel du résultat d'une commande**

`echo "Résultat : $(ls)"` # Exécute `ls` et affiche le résultat


