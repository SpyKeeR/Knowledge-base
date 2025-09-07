# Para/Virtualisation

## **🧱 Virtualisation native (ou complète)**

- Les **machines virtuelles (VM)** communiquent directement avec le **matériel** via l'**hyperviseur**, **sans savoir qu’il est là**.



- L’hyperviseur agit de manière **transparente**, sans modification du système invité.



- Les **instructions binaires** sont **traduites** à la volée par l’hyperviseur vers le matériel.



### 🔍 **Caractéristiques :**

- Aucune modification du noyau de l’OS invité
- Utilise des **techniques d’émulation** ou de **traduction binaire**
- Performances légèrement inférieures à celles du matériel natif



#### 🔧 **Exemple d’architecture :**
VM → Hyperviseur (transparent) → Matériel

#### 🟢 **Avantage :** 
Compatibilité avec tout OS

#### 🔴 **Inconvénient :** 
Traduction → légère baisse de perf



### **🔁 Avant l’arrivée de VT-x / SVM (époque pré-2006)**

- L’hyperviseur devait **traduire** ou **intercepter** certains appels système sensibles 🧩 via une **traduction binaire (binary translation)**.
- Résultat : ⚠️ **perte de performance**, car tout passait par une **émulation logicielle lente**.

#### ➡️ La **para-virtualisation** était alors le meilleur choix :

Les OS invités étaient **modifiés volontairement** pour utiliser des **API dédiées** permettant une **communication directe** avec l’hyperviseur.
👉 C’était **plus rapide**, car on évitait la couche de traduction de la virtualisation native avant VT-X.

### **🚀 Depuis VT-x (Intel) / SVM (AMD)**

- Le processeur prend désormais en charge **nativement** les transitions entre VM et hôte via le **mode VMX (Intel)** ou **SVM (AMD)**.
- Les appels système sensibles sont **gérés matériellement** 💪, **sans traduction**, **sans modification de l’OS invité**.

#### 📦 Résultat :

- On peut faire tourner **n’importe quel OS**, même ceux **non modifiés** (Windows NT, MS-DOS, Linux, etc.)
- La **virtualisation native devient ultra-performante** 🔥
- La **para-virtualisation** est encore utilisé pour certains appels systèmes vers des périphériques virtualisés pour optimiser les performances de ces composants virtuels grâce à l'installation de drivers via les additions invités.

## **🌀 Para-virtualisation**

- Les **VM sont modifiées** pour **savoir qu’elles tournent dans un environnement virtualisé**.
- Elles passent par des **appels système spéciaux** pour **demander** des ressources à l’hyperviseur.
- L’hyperviseur **redirige** ces appels vers le matériel **sans avoir à les traduire**.

### 🔍 **Caractéristiques :**

- Le noyau de l’OS invité doit être **modifié**
- Communication via **API spécialisées**
- Moins d’émulation → **meilleures performances**



#### 🔧 **Exemple d’architecture :** 
VM modifiée → Hyperviseur → Matériel

#### 🟢 **Avantage :** 
Meilleure efficacité

#### 🔴 **Inconvénient :** 
OS invité modifiable seulement (Linux souvent, pas Windows)

