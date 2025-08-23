# Réseaux

## **🏗️ 1️⃣️Principes de base**

📌 Une **machine virtuelle (VM)** doit être reliée à un **switch virtuel** pour communiquer.

📌 Elle utilise une **carte réseau virtuelle** qui fonctionne comme une carte réseau physique.

📌 VMware Workstation propose plusieurs **types de connexion réseau**, chacun avec ses spécificités.



## **🔌 2️⃣️Types de Connexions Réseau**

### **🏠 Host-Only (VMnet1)**

🔹 Communication **uniquement entre les VM** sur l’hôte.

🔹 **Possibilité de communiquer avec l’hôte**.

🔹 **Pas d’accès au réseau extérieur (Internet)**.

🔹 **Utilisation** : Idéal pour des tests en environnement isolé.

### **🔄 VMnet Personnalisé**

🔹 Similaire à **Host-Only**, mais avec une **configuration flexible**.

🔹 Possibilité de **créer plusieurs réseaux isolés** (VMnet0 à VMnet19).

🔹 **VMnet1 = Host-Only** par défaut.

🔹 **VMnet8 = NAT** par défaut.

🔹 **Utilisation** : Reproduction d’architectures complexes en lab.

### **📶 LAN Segments**

🔹 Permet de créer des **réseaux isolés nommés**.

🔹 Aucun accès à l’hôte ou à Internet sans **routeur virtuel**.

🔹 **Utilisation** : Simulation de réseaux d’entreprise segmentés (ex: "Admin", "Serveurs", "Imprimantes").

### **🌍 Bridge (VMnet0)**

🔹 La VM est **directement connectée au réseau physique**.

🔹 Elle obtient une **adresse IP depuis le DHCP du réseau physique**.

🔹 Elle est visible comme un **ordinateur physique** sur le réseau.

🔹 **Utilisation** : Idéal pour intégrer une VM dans un réseau existant.

### **🌐 NAT (VMnet8)**

🔹 La VM accède à Internet via **l’IP de l’hôte** (Translation d’Adresse Réseau).

🔹 Fonctionne comme un **routeur** entre la VM et le réseau physique.

🔹 **Les VM en NAT peuvent communiquer entre elles et avec l’hôte**.

🔹 **Utilisation** : Permet d’avoir **Internet sans être directement exposé** sur le réseau.

