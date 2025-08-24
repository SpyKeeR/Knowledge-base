# G - Substitution, Déplacement, Supression

Une commande ultra-puissante pour **appliquer une action automatiquement** sur **toutes les lignes contenant un motif**.

Tu peux :

- Supprimer les lignes
- Les déplacer
- Remplacer du texte dedans  
  Et tout ça à partir d’une **recherche avec motif** (texte ou expression régulière).

Syntaxe de base : **:g/motif/commande**

ou avec une plage de lignes : **:\[début],\[fin] g/motif/commande**



## **🧹 Supprimer des lignes selon un motif**

**Syntaxe** : **:g/motif/d** → supprime toutes les lignes contenant motif

**:\[début],\[fin] g/motif/d** → idem mais sur une plage de lignes

**Exemple** : **:15,17 g/erreur/d** → supprime les lignes 15 à 17 contenant "erreur"



## **🚚 Déplacer des lignes**

**Syntaxe** : **:[début],[fin] g/motif/m [ligne_destination]**

**Exemple** : **:1,20 g/alerte/m 50** → déplace toutes les lignes contenant "alerte" vers la ligne 50



## **✏️ Substitution conditionnelle (remplacement dans les lignes contenant un motif)**

**Syntaxe** : :[début],[fin] g/motif/s/ancien/nouveau/g

(g = toutes les occurrences sur la ligne)

**Exemples** :

1.  **:30,45 g/demi-dieu/s/Héraclès/Achille/g** → dans les lignes 30 à 45, si "demi-dieu" est présent, remplace tous les "Héraclès" par "Achille"
2.  **:g/Nantes/s//Rennes/g** → sur tout le fichier, dans chaque ligne contenant "Nantes", remplace "Nantes" par "Rennes"
3.  **:g/Niort/s/^#//** → supprime les débuts de ligne commençant par # dans les lignes contenant "Niort" (remplacement par rien)

