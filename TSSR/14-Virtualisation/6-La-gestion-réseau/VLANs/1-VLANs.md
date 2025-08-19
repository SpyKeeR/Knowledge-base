# VLANsUn **VLAN (Virtual Local Area Network)** permet de **séparer logiquement plusieurs réseaux de niveau 2 (Ethernet)** sur une même infrastructure physique.

👉 C’est comme créer plusieurs “réseaux virtuels” indépendants sur un seul switch.



**🧩 Les 3 types d’attribution de VLAN**

**Port-based VLAN**

- 🎯 Affectation manuelle par **port physique** sur le switch.
- Le port est “mappé” à un VLAN spécifique.
- ✅ C’est le **mode le plus courant**, utilisé partout en entreprise.

🔧 Exemple : Port 5 = VLAN 10 → tout ce qui entre par le port 5 est marqué VLAN 10.



**MAC-based VLAN**

- 🎯 Affectation basée sur **l’adresse MAC source**.
- Nécessite un switch qui supporte le **VMPS (VLAN Management Policy Server)**.
- 💡 L’appareil est automatiquement associé à un VLAN en fonction de sa MAC.

🔧 Exemple : MAC 00:11:22:33:44:55 = VLAN 20.



**IP-based VLAN**

- 🎯 Affectation selon **l’adresse IP source**.
- Nécessite un **switch de niveau 3** (L3).
- Rarement utilisé. Peut servir dans des contextes complexes ou réseaux filtrés.

🔧 Exemple : IP 192.168.10.33 = VLAN 30.



**🧱 Structure du tag VLAN 802.1Q (a.k.a. dot1q)**

Quand une trame est envoyée sur un **lien trunk**, elle est “taguée” avec un champ 802.1Q pour indiquer son VLAN d’origine.

Ce champ est **inséré entre la MAC source et le champ EtherType**.



**📐 Tag VLAN = 4 octets (32 bits), décomposé comme ceci :**

| **Champ** | **Bits** | **Description** |
|----|----|----|
| **TPID** | 16 | 0x8100 = identifie que c’est une trame VLAN-tagged |
| **PCP** | 3 | Priority Code Point (QoS, priorité de 0 à 7) |
| **DEI** | 1 | Drop Eligible Indicator (anciennement CFI), peu utilisé |
| **VLAN ID** | 12 | Identifiant du VLAN (valeurs valides : **1 à 4094**) |

🧠 Le **TPID** ne contient **pas** l’ID du VLAN. Il sert juste à signaler la présence d’un tag.
