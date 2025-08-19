# Configuration**⚙️ Création d’un VLAN**

🔹 Étapes en mode global : configure terminal → vlan <vlan-id> → name <vlan-name> → end

➡️ On entre en mode config-vlan pour attribuer un nom. 🧠 Ex : VLAN 20 → name etudiant



**🔌 Affectation d’un port à un VLAN**

🔹 On cible l'interface avec : interface <interface-id> → switchport mode access → switchport access vlan <vlan-id>

➡️ Le port est maintenant dans un VLAN fixe (non dynamique).

🧠 Ex : Port vers VLAN 20 → accès direct au réseau des étudiants.

**🎙️ Configuration VLAN Données + Voix (QoS)**

🔹 Création simultanée : vlan 20 (data) + vlan 150 (voice) → nommés via name.

🔹 Interface configurée :

• switchport mode access

• switchport access vlan 20

• mls qos trust cos → active la QoS

• switchport voice vlan 150 → VLAN voix priorisé

🎯 Résultat : Port utilisé à la fois pour **données classiques + voix IP** sur des VLANs différents, avec **priorisation vocale**.



**👀 Vérification des VLANs**

🔹 Commandes utiles :

• show vlan → liste complète

• show vlan brief → résumé rapide

• show vlan id <id> / name <name> → détails ciblés

• show vlan summary → synthèse générale

🧠 À utiliser pour debugger ou valider la config en cours.



**❌ DTP – Désactiver le Dynamic Trunking Protocol**

**⚙️ But :** Par défaut, les interfaces peuvent **négocier automatiquement** un lien trunk avec un autre switch (via DTP).

→ Risque de trunk non souhaité si l'autre côté est en mode dynamique.

**🔒 Solution : Désactiver DTP avec :** switchport nonegotiate

➡️ À combiner avec une config explicite du mode :

- switchport mode access OU switchport mode trunk

🧠 À noter : la commande nonegotiate **ne fonctionne que si le port n’est pas en mode dynamique** (auto/desirable). Elle **n’empêche pas l’envoi de trames VLAN**, mais **coupe la négociation DTP**.
