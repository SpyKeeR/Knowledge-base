## **🎯 Comment Fonctionne la Notation CIDR ?**

Une adresse en notation CIDR est composée de **deux éléments** :

1️⃣ **L’adresse réseau** : Exemple 192.168.1.0

2️⃣ **Le préfixe CIDR** (/24, /26...) : Indique le **nombre de bits réservés à la partie réseau**.

👉 **Exemple** : 192.168.1.0/24

- /24 signifie que les **24 premiers bits** identifient le **réseau**.
- Les **8 bits restants** sont réservés aux **hôtes**.

| **CIDR** | **Masque Décimal** | **Bits Réseau** | **Bits Hôte** | **Hôtes Utilisables** |
|----|----|----|----|----|
| /24 | 255.255.255.0 | 24 | 8 | 254 |
| /26 | 255.255.255.192 | 26 | 6 | 62 |
| /28 | 255.255.255.240 | 28 | 4 | 14 |

✅ **Plus le masque est grand (/30, /31...), plus il y a de bits réseau, et moins il y a d’hôtes possibles.**



## **📌 Pourquoi Utiliser CIDR ?**

🔹 **Optimisation de l’espace IPv4** : Permet de créer des **sous-réseaux adaptés** sans gaspiller d’adresses.

🔹 **Plus de flexibilité** : On peut allouer des **blocs de tailles variées** (/28, /29...) selon les besoins.

🔹 **Facilite l’agrégation** : Un groupe d’adresses peut être résumé sous une seule entrée (supernetting).



## **🔢 Conversion CIDR ↔ Masque Décimal**

Chaque notation /n correspond à un **masque de sous-réseau** en **décimal**.

- /24 → 255.255.255.0 → (24 bits à 1, 8 bits à 0)
- /26 → 255.255.255.192 → (26 bits à 1, 6 bits à 0)
- /28 → 255.255.255.240 → (28 bits à 1, 4 bits à 0)

### **Calcul rapide des adresses réseau et broadcast avec la méthode du Nombre Magique** :

1️⃣ **Identifier l’octet du masque qui contient encore des bits hôte**.

2️⃣ **Calculer le Nombre Magique** : 256 - cet octet du masque

3️⃣ **Lister les multiples du Nombre Magique** pour identifier les plages réseau.
