# 📦 **🔁 Boucle foreach — Parcourir un tableau ou une collection**

Structure utilisée pour parcourir **chaque élément** d’un tableau (Array) ou d’une collection (ArrayList).

Forme : `foreach ($élément in $collection) { ... }` 

➡️ $élément contient la **valeur actuelle** de la boucle, **change automatiquement** à chaque tour.



## 🧮 **📋 Fonctionnement général**

- S’utilise uniquement avec des variables de type Array, ArrayList, ou objets multiples.
- À chaque tour, la variable prend la **valeur suivante** de la liste.
- La boucle se termine **automatiquement** quand tous les éléments ont été parcourus.
- Nombre de tours = nombre d’éléments dans la collection.
- Aucun contrôle d’index requis, c’est le moteur qui gère.



## 🧑‍💻 **Exemple simple sur tableau texte**

```powershell
$liste = "PC1", "PC2", "PC3"
foreach ($pc in $liste) {
    Write-Host "Le PC $pc est en ligne"
    }
```
→ Affiche pour chaque machine son état.

## 📡 **🗂 Traitement d’objets complexes**

Exemple : récupération d’utilisateurs avec Get-ADUser.

```powershell
$users = Get-ADUser -Filter *
foreach ($user in $users) {
    Write-Host $user.Name
    } 
```
→ $user contient à chaque tour **un objet AD**, on peut accéder à ses **propriétés** : .Name, .GivenName, etc.

## 🧩 **🔎 Accès aux propriétés de l’objet courant**
```powershell
foreach ($user in $users) {
    Write-Host "Nom : $user.Name - Prénom : $user.GivenName"
    }
```
→ On affiche plusieurs propriétés à chaque tour.
→ $user contient un **objet utilisateur complet**, pas une simple chaîne.



## 🛑 **🔧 Commandes de contrôle dans la boucle**

1.  `break` : quitte **immédiatement** la boucle.  
    Exemple : `if ($user.Name -eq "administrateur") { break }` → La boucle s’arrête dès que cette condition est vraie.

2.  `continue` : **ignore l’itération en cours**, passe à la suivante.  
    Exemple : `if ($user.Name -eq "judaCameron") { continue }` → Cet utilisateur ne sera pas affiché.



## 📌 **🧠 À retenir**

- foreach est idéal pour **parcourir des données** sans se soucier des index.
- Compatible avec **tout objet itérable** : tableau, résultat de commande, liste de fichiers...
- $i, $pc, $user sont des **pointeurs temporaires** utilisés dans la boucle.
- Chaque itération = **traitement isolé d’un élément**, possibilité de conditionner l’action.
- Peut être combiné avec des tests if, ou commandes break, continue.



