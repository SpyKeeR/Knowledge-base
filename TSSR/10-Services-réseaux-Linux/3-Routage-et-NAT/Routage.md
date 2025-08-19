# RoutageLe **routage**, c’est le processus qui permet à une machine de **communiquer avec d’autres machines situées sur des réseaux différents**.

👉 Une machine utilise son **adresse IP** + **masque de sous-réseau** pour déterminer si le destinataire est sur le même réseau. Si ce n’est pas le cas ➜ elle consulte sa **table de routage** pour savoir par quelle **passerelle** (souvent un **routeur**) elle doit passer.



📡 **Mise en place du routage**

Un réseau d’entreprise peut être composé de plusieurs sous-réseaux :

➡ Pour communiquer entre eux ou sortir vers Internet, il faut **définir des routes**.

🛣 Ces routes sont **installées sur les routeurs**, et non sur les postes de travail (qui ne connaissent que leur **passerelle par défaut**).

🔁 Si deux machines sont sur le **même segment réseau**, pas besoin de routage.

🚪 Si elles sont sur des **réseaux différents**, il faut passer par un **routeur interne ou une passerelle par défaut**.



📑 **Types de routes**

1.  **Route d’hôte** : vers une seule IP (rare).
2.  **Route de réseau** : vers un sous-réseau complet.
3.  **Route par défaut** : utilisée quand aucune route ne correspond (souvent vers la box ou le pare-feu).



⚙️ **Gestion dynamique (commande ip route)**

💡 Permet d’ajouter, supprimer ou modifier des routes **temporairement**. 📌 Attention : ces routes **disparaissent au redémarrage**.

🔍 Affichage des routes : ip route show

➕ Ajout : vers un hôte : ip route add 10.11.12.3 via 172.16.6.3

- vers un réseau : ip route add 10.56.0.0/16 via 172.16.6.253
- par défaut : ip route add default via 172.16.6.1  
  🔁 Modification : ip route change ...  
  ❌ Suppression : ip route del ...



💾 **Gestion statique (persistante)**

Pour conserver les routes après reboot, deux options :

📤 Export du script d’ajout avec ip route et exécution automatique au boot.

📂 Ou mieux : les définir dans le fichier **/etc/network/interfaces** ➜ méthode **préférée et durable**. (pre-up/up et post-up/pre-down et down/post-down)



🚦 **Activer une machine Linux comme routeur**

Par défaut, un Linux **ne fait pas de routage** (il ne transmet pas les paquets entre ses interfaces).

🔧 Pour l’activer :

1.  Éditer /etc/sysctl.conf ➜ ajouter/modifier net.ipv4.ip_forward = 1
2.  Appliquer le changement : sysctl -p
3.  Vérifier : sysctl net.ipv4.ip_forward ➜ si 1 = activé / 0 = désactivé
