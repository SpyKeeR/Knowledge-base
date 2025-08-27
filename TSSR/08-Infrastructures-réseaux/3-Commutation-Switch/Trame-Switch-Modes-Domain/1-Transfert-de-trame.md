# Transfert de trame

### **🧠 Étape 1 – Apprentissage de l'adresse MAC source**

Quand une trame arrive :

- Si l’adresse **MAC source est inconnue**, 👉 **ajout dans la table MAC** associée au port.
- Si elle est déjà connue mais sur **un autre port**, 👉 **mise à jour de l’entrée existante**.
- Si elle est connue sur le **même port**, 👉 **aucune action** (compteur mis à jour).

🔁 **But** : Suivre dynamiquement qui est où, pour éviter le broadcast inutile.



### **📦 Étape 2 – Analyse de l'adresse MAC de destination**

- Si **@MAC de destination est une unicast connue** 👉 **envoyée uniquement sur le port correspondant**.
- Si l’adresse **n’est pas présente** 👉 trame **envoyée sur tous les ports** (flood).
- ⚠️ Les trames broadcast et multicast sont **toujours diffusées à tous les ports**, sauf au port d'origine.



## **🚀 Méthodes de transfert de trames Ethernet**

### **🗂️ Méthode Store-and-Forward**

- Le switch **attend d’avoir reçu toute la trame**, la stocke en mémoire, puis :
  - Il **vérifie l’intégrité** via le champ FCS (Frame Check Sequence).
  - Si OK ✅ → la trame est transférée.
  - Si NOK ❌ → la trame est **jetée**.

📌 Avantages : • **Détection d’erreurs FCS** • **Transmission inter-vitesse** possible

📌 Inconvénients : • **Latence plus élevée** • Besoin de **plus de mémoire tampon**



### **⚡ Méthode Cut-through**

- Le switch **n’attend pas** la trame complète. Dès que l’adresse MAC de destination est lue : La trame est **immédiatement transférée**.

📌 Avantages : • **Latence ultra-faible** • **Réactivité optimale**

📌 Inconvénients : • **Pas de vérification FCS** • **Propagation possible d’erreurs** • Tous les ports doivent être à **vitesse identique**



### **⚔️ Comparatif rapide**

| **🧪 Méthode**    | **⏱️ Latence** | **✔️ Intégrité** | **⚡ Conditions**    |
|-------------------|----------------|------------------|----------------------|
| Store-and-Forward | Haute          | ✅ Oui (FCS)     | Ports mixtes         |
| Cut-through       | Très faible    | ❌ Non           | Ports mêmes vitesses |



