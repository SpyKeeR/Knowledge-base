# Multicast sollicited (NDP)**📚 Multicast Sollicité en IPv6**

- **But** : Résolution des adresses MAC, évite les **broadcasts** comme en IPv4 (ARP).
- **Structure** : FF02::1:FFXX:XXXX
  - **XX:XXXX** : 24 derniers bits de l’adresse IPv6 cible.



**🔄 Détail du Processus NDP avec Multicast Sollicité**

1️⃣ **PC A veut envoyer un paquet à PC B** :

- Destinataire : 2001:db8::1:2b3c:4d78.
- PC A ne connaît pas l'adresse **MAC** de PC B.

2️⃣ **Génération de l'adresse multicast sollicitée** :

- Le PC A génère l'adresse multicast sollicitée à partir des 24 derniers bits de l'adresse IPv6 cible :  
  **IPv6 Multicast Sollicité** : **FF02::1:FF3C:4D78**.

  - **Adresse MAC multicast** : **33:33:FF:3C:4D:78**.

3️⃣ **Envoi du message Neighbor Solicitation (NS)** :

- **Destination** : FF02::1:FF3C:4D78.
- Paquet encapsulé avec l'adresse **MAC multicast** 33:33:FF:3C:4D:78.
- Envoi en **multicast** pour que seul le destinataire écoute.

4️⃣ **Réception et réponse de PC B** :

- PC B, avec l'adresse IPv6 2001:db8::1:2b3c:4d78, écoute cette adresse multicast.
- Il répond avec un **Neighbor Advertisement (NA)**, contenant sa **vraie adresse MAC**.

5️⃣ **Mise à jour de la table NDP** :

- PC A enregistre la correspondance dans sa **table NDP (Neighbor Cache)**.
- La communication est maintenant prête à être établie.



**🛠 Avantages**

- **Optimisation** du réseau : pas de **broadcasts**.
- **DAD** (Détection des adresses dupliquées) : Vérifie la **unicité** des adresses.
- **Découverte MAC** : Résolution des adresses MAC via **Solicited-Node Multicast**.
- **Efficacité** : Réduit la pollution réseau et améliore la scalabilité.
