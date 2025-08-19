# Statuts 🖥️ **Statuts des *hôtes (hosts)***

Un hôte, c’est une machine physique ou virtuelle qu’on supervise. Il peut avoir **3 statuts principaux**, chacun codé par un chiffre :

- **UP (0)** ➤ Hôte joignable, RAS
- **DOWN (1)** ➤ Hôte injoignable, machine ou réseau off
- **UNREACHABLE (2)** ➤ Hôte inaccessible car un parent (ex : routeur/switch) est DOWN ou l’arborescence rend l’hôte non atteignable

📌 **Important :** Quand tu écris une sonde personnalisée, tu dois retourner le **code de statut correspondant** à la situation détectée sur l’hôte.



🛠️ **Statuts des *services***

Un service, c’est ce que tu veux observer sur l’hôte (CPU, RAM, HTTP, etc). Il peut avoir **5 statuts distincts**, là encore avec des codes bien précis :

- **OK (0)** ➤ Tout roule, service fonctionnel
- **WARNING (1)** ➤ Dépassement d’un seuil d’alerte, mais sans panne
- **CRITICAL (2)** ➤ Dysfonctionnement majeur, seuil critique dépassé
- **UNKNOWN (3)** ➤ Impossible de conclure : erreur dans le plugin, connexion HS, ou info manquante
- **PENDING (4)** ➤ Données en attente, le moteur n’a pas encore assez d’éléments pour juger (souvent lors du premier check)

🧠 **Remarque :** Seuls les statuts **0 à 3** sont considérés comme traitables directement. Le statut **PENDING** disparaît dès qu'une première donnée fiable est obtenue.



📈 **Usage dans le moteur de supervision**

Le moteur (Centreon Engine ou Nagios) ne fait **que lire les codes** de retour des commandes et les **interprète** comme des statuts. Ensuite, en fonction :

- il **change la couleur de l’interface**,
- il **déclenche des alertes ou des actions**,
- il **log l’état** dans l’historique.

💡 Donc, que tu sois en train de superviser un serveur web ou un switch réseau, **les statuts sont ta base de communication avec l'outil**.
