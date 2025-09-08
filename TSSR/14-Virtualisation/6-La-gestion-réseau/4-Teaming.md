# Teaming

Le **teaming** (ou agrégation de cartes réseau) consiste à **regrouper plusieurs cartes réseau (VMNIC)** d’un hôte ESXi dans un même vSwitch virtuel, afin de :

- 🔄 **Assurer la continuité de service** en cas de panne
- 🚀 **Améliorer la performance réseau** (bande passante accrue)



## **🛡️ Importance du Teaming**

🔸 Permet d'avoir une **solution de secours** en cas de défaillance matérielle (NIC ou hôte)

🔸 Garantit une **meilleure disponibilité** et **tolérance aux pannes** dans un environnement virtualisé



## **⚙️ Stratégies de Teaming**

### **🔁 Débit Cumulé (Actif-Actif)**

- Utilise **au moins deux cartes réseau actives**
- Les débits sont **additionnés** pour augmenter la bande passante
- ✅ Meilleure performance
- ⚠️ En cas de panne d’une carte, **la bande passante est réduite de moitié**



### **💤 Tolérance de Panne (Actif-Passif)**

- Une carte est **active**, l’autre en **veille**
- Si la carte active échoue, la carte passive **prend le relais automatiquement**
- ✅ Résilience automatique ⚠️ Pas de gain de performance : **la carte passive reste inutilisée en fonctionnement normal**



## **🧱 Mise en œuvre du Teaming**

📌 **Prérequis** : Minimum **2 VMNIC** connectées à un **vSwitch standard**

🔧 Configuration possible : Lors de la **création du vSwitch** Ou par **ajout ultérieur** de cartes réseau à un groupement de ports existant



## **🧠 Répartition de Charge (Load Balancing)**

Permet de personnaliser comment le trafic réseau est distribué entre les cartes réseau disponibles :

### **👤 Par VM**

- Chaque port du vSwitch (lié à une VM) est associé à une **VMNIC dédiée**

### **🆔 Par adresse MAC**

- Le trafic est orienté selon l’**adresse MAC source** des paquets

### **📦 Par paquet**

- Un algorithme choisit la VMNIC la plus adaptée **selon l'adresse source et destination** du paquet

### **📋 Par ordre (Ordonnancement)**

- Une carte réseau est utilisée en priorité ; les autres sont **activées uniquement en cas de défaillance**

