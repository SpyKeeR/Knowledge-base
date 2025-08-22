# Plages inutilisés

## **Exemple : Trouver les plages libres dans 199.1.1.0/24**

On liste d'abord les réseaux occupés :

- 199.1.1.0 /26 (199.1.1.0 → 199.1.1.63)
- 199.1.1.128 /27 (199.1.1.128 → 199.1.1.159)
- 199.1.1.160 /27 (199.1.1.160 → 199.1.1.191)
- 199.1.1.192 /28 (199.1.1.192 → 199.1.1.207)
- 199.1.1.224 /28 (199.1.1.224 → 199.1.1.239)
- 199.1.1.240 /28 (199.1.1.240 → 199.1.1.255)

### 👉 **Les plages non utilisées sont entre ces blocs** :

- **199.1.1.64 → 199.1.1.127** (Plage de 64 adresses = sur 6 bits = 32-6 = /26 libre)
- **199.1.1.208 → 199.1.1.223** (Plage de 16 adresses = sur 4 bits = 32-4 = /28 libre)

### ✅ **Sous-réseaux disponibles :**

- **199.1.1.64/26**
- **199.1.1.208/28**



## **Exemple : Trouver les sous-réseaux libres dans un réseau avec plusieurs segments**

### **🔹 Données : Réseau de classe C**

On doit découper un réseau de **256 adresses (/24)** pour :

- **1 segment de 62 postes** (sur 6bits [32-6] nécessite /26 → 64 adresses)
- **3 segments de 30 postes** (sur 5bits [32-5] nécessite /27 → 32 adresses chacun)
- **4 segments de 14 postes** (sur 4bits [32-4] nécessite /28 → 16 adresses chacun)

### 📌 **Découpage :**

- **1er bloc :** 192.168.1.0/26 (62 postes)
- **3 suivants :** 192.168.1.64/27, 192.168.1.96/27, 192.168.1.128/27 (3 x 30 postes)
- **4 suivants :** 192.168.1.160/28, 192.168.1.176/28, 192.168.1.192/28, 192.168.1.208/28 (4 x 14 postes)

### 👉 **Sous-réseaux inutilisés :**

- **192.168.1.224/27 (32 adresses disponibles)**
