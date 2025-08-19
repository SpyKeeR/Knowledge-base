# Entre deux PC10:32



**🎯 Cas 1 : Ping entre Deux Postes sur le Même Réseau**

📌 Si **les deux postes sont dans le même sous-réseau**, ils peuvent **communiquer directement** sans passer par une passerelle.

📌 Le ping fonctionne immédiatement **si les adresses IP sont bien configurées** et dans le même sous-réseau.

📌 Si un poste ne connaît pas l'adresse MAC de l'autre, il envoie une **requête ARP** en **broadcast** (FF:FF:FF:FF:FF:FF).

📌 Une fois l'adresse MAC obtenue, la communication se fait directement **via des trames Ethernet**.



**🌍 Cas 2 : Ping entre Deux Postes sur des Réseaux Différents**

🔹 Si les deux postes sont **dans des réseaux différents**, une **passerelle (routeur)** est nécessaire.

🔹 **Condition** : La passerelle **doit être dans le même réseau que le poste émetteur**.

**🔄 Étapes de la Communication via la Passerelle**

1️⃣ **Le poste source envoie une requête ARP** pour obtenir l'adresse MAC de la passerelle (car il ne peut pas envoyer directement au poste cible).

2️⃣ **La passerelle répond avec son adresse MAC**, que le poste stocke dans son **cache ARP**.

3️⃣ **Le poste source envoie ensuite une trame Ethernet** à la **passerelle**, avec :

- **MAC source :** MAC du poste / **MAC destination :** MAC de la passerelle / **IP destination :** IP du poste cible

4️⃣ **La passerelle route le paquet vers le bon réseau** et effectue le même processus ARP du côté du destinataire.  
5️⃣ **La réponse suit le chemin inverse**.

**🔄 Optimisation via le Cache ARP**

📌 **Le cache ARP stocke temporairement** les adresses MAC obtenues pour éviter d’envoyer une requête ARP à chaque fois.

📌 Cela permet d’**accélérer la communication** tant que l’entrée ARP est valide.

