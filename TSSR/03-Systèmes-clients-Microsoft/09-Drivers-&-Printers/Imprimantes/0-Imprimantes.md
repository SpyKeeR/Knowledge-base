# Imprimantes

## 📌 **🔹 Périphérique d'impression vs Imprimante**

- **Périphérique d’impression** → Le matériel 📟 (ex: imprimante physique)
- **Imprimante** → La partie **logicielle** qui envoie les ordres d'impression



## 📌 **🛠️ Composition d’une imprimante (côté logiciel)**

- **Pilote** → Interface entre Windows et le périphérique
- **Configuration** → Paramètres par défaut (recto-verso, N&B...)
- **File d’attente d’impression** 📜 → Gérée par le **service Spooler** 🌀
  - Transmet au driver et convertit le document en un format compréhensible pour l’imprimante
  - Gère les travaux d’impression en file
- Port de connexion → **USB, LPT, COM, IP…**



## 📌 **📍 Types d’imprimantes**

### 🔹 **Imprimante locale**

- Connectée **directement** au PC.
- Peut être **partagée sur le réseau** (⚠️ l'hôte doit rester allumé)



### 🔹 **Imprimante réseau 🌐**

- **Autonome** avec sa propre **carte réseau**
- Accessible à plusieurs utilisateurs simultanément
- Peut intégrer un **serveur web** pour une gestion avancée (souvent sur les copieurs pro)



## 📌 **⚙️ Gestion & configuration**

🔹 **Ajout d’une imprimante** → Panneau de config ou Win + I > Périphériques > Imprimantes et scanners

🔹 **Gestion du Spooler** → services.msc → "Spouleur d'impression"

🔹 **Connexion à une imprimante réseau** → Via son IP ou en cherchant sur le réseau

