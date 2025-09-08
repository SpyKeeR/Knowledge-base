# Trunking


## **🧱 Introduction aux VLANs et au Trunking**

- Les **VLANs** (Virtual Local Area Networks) permettent de **segmenter le réseau** pour isoler les flux (postes clients, téléphonie, serveurs…).
- Les **trunks** sont utilisés pour **transporter le trafic de plusieurs VLANs** entre des équipements réseau, notamment :
  - 🔌 **Entre commutateurs physiques**
  - 🧩 **Entre commutateurs physiques et virtuels (vSwitch/VDS)**



## **🔗 Fonctionnement des trunks**

- Un **port trunk** permet de faire transiter plusieurs VLANs **sur une seule liaison physique**.
- Contrairement à un port en mode **Access** (lié à un seul VLAN), le trunk accepte **un ensemble de VLANs**.
- Il permet la **communication VLAN ↔ VLAN** entre plusieurs switchs ou équipements.



## **🎛️ Paramètres de configuration essentiels**

- **Type de port :**
  - 🔒 Access : un seul VLAN, pour équipement terminal (ex : PC, imprimante)
  - 🔀 Trunk : plusieurs VLANs, pour interconnexion entre switchs ou vers hyperviseur
- **Marquage VLAN (802.1Q) :**
  - 🟢 **Actif** : champ **TAG VLAN** propagé dans les trames sortantes
  - 🔴 **Inactif** : le tag est **supprimé** avant d’envoyer la trame
  - Par défaut, le **VLAN 1** est utilisé sur tous les ports sauf configuration spécifique



## **🖧 Trunking et Virtualisation**

- Les **vSwitch/VDS** peuvent être configurés avec des **IDs VLANs** pour chaque interface réseau.
- Certains systèmes d’exploitation ou pilotes **supportent le marquage VLAN** :
  - 💻 OS compatibles 802.1Q
  - 📶 Pilotes de cartes réseau
  - ☎️ Téléphones IP
- Ces équipements doivent être connectés à un **port trunk**.

