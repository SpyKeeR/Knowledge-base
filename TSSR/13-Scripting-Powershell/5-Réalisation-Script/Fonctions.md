# Fonctions- Une **fonction** est un bloc de code réutilisable.
- Elle sert à **organiser**, **simplifier**, et **éviter la répétition** dans un script PowerShell.
- Créer une fonction, c’est comme créer **une nouvelle commande personnalisée**.
- PowerShell **n’est pas sensible à la casse** (fonction, Fonction, FUNCTION = même chose).



**🛠️ Déclaration d’une fonction**

- Mot-clé function suivi d’un **nom sans espace** ni ponctuation.
- Le code à exécuter est placé **entre accolades {}**.
- Les **paramètres** sont déclarés avec param().

function addition {  
param ($a, $b)  
$c = $a + $b  
return $c  
}

💬 Appel de la fonction : $resultat = addition 1 2 👉 La fonction retourne ici la somme de 1 et 2.



**🎛️ Paramètres des fonctions**

- Les fonctions acceptent des **paramètres** comme n’importe quelle commande.
- Ils sont définis dans le bloc param(), lors de la déclaration de la fonction.
- Si **aucun paramètre n’est fourni**, la fonction peut ne rien retourner (ou provoquer une erreur selon le code).



**🔐 Paramètres obligatoires**

- On peut rendre un paramètre **obligatoire** avec l’attribut [Parameter(Mandatory=$true)].

function maFonction {  
param (  
[string]$monparametre,  
[Parameter(Mandatory=$true)][string]$autreparametre  
)  
}

🛑 Si $autreparametre n’est pas renseigné → **la fonction ne s’exécute pas**.



**📄 Bonnes pratiques**

- Déclarer les fonctions **en début de script**.
- Utiliser des **noms explicites** pour la clarté (ex : Get-InfosPC, Check-Connexion).
- Éviter les abréviations trop obscures.

**🔍 Exemple avec contexte métier : fonction find**

💡 Rechercher un utilisateur dans l’Active Directory :

function find {  
param($u)  
$r = Get-ADUser -Filter { Name -eq $u }  
if ($null -eq $r) {  
return "L'utilisateur $u n'existe pas."  
}  
return $r  
}

💬 Appel :

- find "accelerios" → retourne les infos si l’utilisateur existe.
- find "marc" → retourne "L'utilisateur marc n'existe pas."



**📌 À retenir**

- ✅ Les fonctions améliorent la **lisibilité**, la **modularité**, et la **réutilisabilité** du code.
- ✅ Elles peuvent recevoir des **paramètres simples ou avancés**.
- ✅ On peut contrôler leur comportement avec des **paramètres obligatoires**.
- ✅ Chaque fonction peut retourner une **valeur ou un message** à l’appelant.
