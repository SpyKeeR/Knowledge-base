# Calcul d'adresse reseau

Une adresse IPv4 se compose de :

🔹 **L’ID réseau** : Identifie le réseau.

🔹 **L’ID hôte** : Identifie un appareil dans ce réseau.

Le **masque de sous-réseau** permet de séparer ces deux parties. Il est écrit en notation CIDR (ex. /24) et indique le **nombre de bits dédiés à la partie réseau**.



## **🎯 Méthode pour Déterminer les Adresses Réseau et Broadcast**

### **📌 1. Trouver l’Adresse Réseau**

L’**adresse réseau** est obtenue **en remplaçant tous les bits de la partie hôte par 0**.  
🔹 **En pratique** : c’est le **résultat d’une opération logique AND entre l’adresse IP et le masque**.

### **📌 2. Trouver l’Adresse Broadcast**

L’**adresse de broadcast** est obtenue **en remplaçant tous les bits de la partie hôte par 1**.  
🔹 **En pratique** : on applique un **XOR entre l’adresse réseau et l’inverse du masque** (NOT masque).



## **🔢 Méthode du Nombre Magique (Sans Conversion Binaire)**

Plutôt que de convertir en binaire, on peut rester en **notation décimale** grâce au **nombre magique**.

### **Méthode :**

1️⃣ **Trouver l’octet du masque** qui n’est pas 255 (celui qui contient encore des bits hôte).

2️⃣ **Calculer le nombre magique** : **256 - cet octet du masque**

3️⃣ **Identifier les plages d’adresses réseau** en utilisant ce multiple.

### 💡 **Exemple avec 192.168.1.10/26**

🔹 Masque : 255.255.255.192 🔹 Octet clé : 192

🔹 Nombre magique : **256 - 192 = 64** 🔹 Plages possibles : **0, 64, 128, 192**

L’adresse **192.168.1.10** se situe entre 192.168.1.0 et 192.168.1.63.

| **Élément**           | **Valeur**   |
|-----------------------|--------------|
| **Adresse Réseau**    | 192.168.1.0  |
| **Adresse Broadcast** | 192.168.1.63 |

✅ **Astuce : Les adresses réseau sont toujours des multiples du nombre magique.**
