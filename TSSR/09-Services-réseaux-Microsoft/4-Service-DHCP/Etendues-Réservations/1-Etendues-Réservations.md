# Etendues/Réservations**📦 Qu’est-ce qu’une étendue ?**

Une **étendue DHCP** (*scope*) est un conteneur logique 🧺 qui regroupe toutes les infos nécessaires à l’attribution d’adresses IP dans un segment réseau.

Elle est définie par un **nom** (ex : *LAN Serveur*) et une **plage d’adresses IP disponibles**.

➡️ C’est dans cette étendue que les clients viendront piocher leur configuration IP 🧃



**📐 Caractéristiques d’une étendue**

Une étendue contient :

- 🧾 **Nom** de l’étendue (ex : *PostesBureau*, *Imprimantes*, etc.)
- 🌐 **Plage d’adresses IP** (ex : 192.168.0.1 à 192.168.0.254)
- ⏳ **Durée du bail** (ex : 2 jours → la validité d’une IP attribuée)
- 🚫 **Plages d’exclusion** (ex : 192.168.0.5 à 192.168.0.10 → jamais attribuées)

🔎 Une IP dans la plage d’exclusion = IP qu’on souhaite réserver pour autre chose (serveur, switch, borne Wi-Fi…)



**📌 La réservation d’adresse IP**

Une **réservation** permet d’associer **de manière fixe** une IP à une **adresse MAC** spécifique 🖇️

➡️ Idéal pour des équipements critiques (serveurs, imprimantes, caméras…) qui doivent toujours avoir la **même IP**.

**Exemple** :

- 🔖 Nom : *SRVFIC*
- 💻 Adresse IP réservée : 192.168.0.1
- 🧬 Adresse MAC : liée au poste cible
- 🗂️ Conteneur parent : l’étendue dans laquelle cette réservation est définie

La réservation empêche l’IP d’être attribuée à un autre poste 🛡️
