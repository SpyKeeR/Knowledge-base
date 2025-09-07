# Paramétrages matériels

## **🔧 Accès aux paramètres matériels**

- Après création d’une VM, les réglages sont accessibles via **Virtual Machine Settings**
  - → Menu **Edit > Virtual Machine Settings**
  - → Ou clic droit sur l’onglet de la VM

- Permet :
  - L’**ajout ou suppression de matériel**
  - La **modification des paramètres initiaux**
- Certains composants sont **modifiables à chaud** (sans arrêt de la VM) :
  - 🧠 Mémoire vive (RAM)
  - 🌐 Cartes réseau
  - 💾 Disques supplémentaires (dans certains cas)



## **🌐 Connexions réseau — 4 modes disponibles**

### **🔗 Bridged (Mode pont)**

- La VM est connectée **directement au réseau physique** de l’hôte
- Avantages :
  - Accès complet à Internet et aux services réseau (DHCP, DNS, etc.)
- Inconvénient :
  - ⚠️ **Exposition directe** au réseau = risque de sécurité accru



### **🌍 NAT (Network Address Translation)**

- La VM partage la connexion réseau de l’hôte via un mécanisme NAT
- Avantages :
  - Accès à Internet
  - 🛡️ Isolation de la VM = sécurité renforcée
- Géré **entièrement par VMware Workstation**



### **🏠 Host-only**

- Communication **exclusivement** :
  - Entre les VM configurées en Host-only
  - Entre l’hôte et les VM
- Pas d'accès à Internet ou au réseau externe
- Idéal pour des tests **locaux** sans sortir du poste physique



### **🧱 LAN Segment**

- Création de **réseaux virtuels isolés**, sans lien avec l’hôte
- Chaque **LAN Segment** agit comme un **commutateur privé virtuel**
- Utilisé pour :
  - Simuler des topologies complexes
  - Cloisonner les flux entre différentes VM
- La configuration se fait via le bouton **LAN Segments** dans les paramètres réseau


