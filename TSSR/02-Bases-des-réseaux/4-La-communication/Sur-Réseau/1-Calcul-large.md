## **1️⃣ Identifier la plage de réseaux à supernetter**

On a une plage de réseaux **de 212.1.96.0/24 à 212.1.159.0/24**, donc on doit inclure **tous les sous-réseaux entre ces deux extrémités**.



## **2️⃣ Calculer le nombre total de réseaux /24 à couvrir**

L'écart entre les deux adresses réseau est : 159 - 96 = 63

On doit couvrir **64 réseaux** car : 63 + 1 = 64

On cherche le plus petit supernet pouvant contenir **ces 64 réseaux consécutifs**.



## **3️⃣ Vérifier si 96 est un multiple des tailles de supernet candidates**

### **🔹 Test avec /18 (taille = 64 réseaux)**

Un supernet /18 couvre le scope d'adresse de **64 réseaux /24** donc on doit vérifier si **96 est un multiple de 64** : 96 ÷ 64 = 1.5

#### ⛔ Problème : 1.5 n'est pas un entier → **96 n'est pas un multiple de 64**.

#### **Plage couverte par /18** :

- Le premier multiple de 64 inférieur à 96 est **64**.
- **212.1.64.0/18 couvre de 212.1.64.0 à 212.1.127.255**.
- ❌ **Notre plage 212.1.96.0 à 212.1.159.0 n'est pas entièrement incluse !**

#### ✅ **On doit essayer un masque plus grand**.


### **🔹 Test avec /17 (taille = 128 réseaux)**

Un supernet /17 couvre le scope d'adresse de **128 réseaux /24**. On doit vérifier si **96 est un multiple de 128** : 96 ÷ 128 = 0.75

#### ⛔ Problème : 0.75 n'est pas un entier → **96 n'est pas un multiple de 128**.

#### **Plage couverte par /17** :

- Le premier multiple de 128 inférieur à 96 est **0**.
- **212.1.0.0/17 couvre de 212.1.0.0 à 212.1.127.255**.
- ❌ **Encore trop petit, la fin de notre plage 212.1.159.0 n'est pas couverte !**

#### ✅ **On doit essayer un masque plus grand**.


### **🔹 Test avec /16 (taille = 256 réseaux)**

Un supernet /16 couvre le scope de **256 réseaux /24**. On doit vérifier si **96 est un multiple de 256** : 96 ÷ 256 = 0.375

#### ⛔ Problème : 0.375 n'est pas un entier → **96 n'est pas un multiple de 256**.

#### **Plage couverte par /16** :

- Le premier multiple de 256 inférieur à 96 est **0**.
- **212.1.0.0/16 couvre de 212.1.0.0 à 212.1.255.255**.
- ✅ **Cette fois, notre plage 212.1.96.0 à 212.1.159.0 est incluse !**
