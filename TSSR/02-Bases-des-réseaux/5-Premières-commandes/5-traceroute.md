# traceroute

## **Définition**

- tracert sous Windows et traceroute sous Linux permettent d’**analyser le chemin des paquets** entre une machine et une destination.
- Ils identifient **les routeurs traversés**, mesurent la **latence**, et aident à **diagnostiquer les problèmes réseau**.

## **⚙️ Syntaxe**

- **Windows** : tracert [options] <adresse-cible> / **Linux** : traceroute [options] <adresse-cible>

## **🔍 Fonctionnement**

- tracert utilise **ICMP**, tandis que traceroute utilise **UDP par défaut** (ICMP avec -I).
- Envoie des paquets avec un **TTL croissant**, chaque routeur renvoyant un message ICMP **TTL exceeded**, permettant d’identifier le chemin parcouru.

## **📊 Exemple de sortie**

Chaque ligne affiche :

- Numéro du saut (**hop**)
- Temps de réponse en millisecondes (**latence**)
- Adresse IP ou nom DNS du routeur

Si une ligne affiche * * *, le routeur ne répond pas (filtrage ICMP ou congestion).

## **🛠️ Options principales**

- /d (Windows) ou -n (Linux) : Désactive la résolution DNS (affiche seulement les IP).
- /h <nombre> ou -m <nombre> : Définit un **TTL maximum**.
- /w <ms> : Définit un **temps d’attente** pour chaque saut.
- /4 et /6 : Force **IPv4** ou **IPv6**.
- -I (Linux) : Utilise **ICMP au lieu d’UDP** (équivalent Windows).

## **🚧 Limitations**

- Certains routeurs ou pare-feu **bloquent ICMP** (* * * dans les résultats).
- Les **chemins peuvent varier** selon le routage dynamique.
- Un **routeur surchargé** peut ne pas répondre dans le temps imparti.
