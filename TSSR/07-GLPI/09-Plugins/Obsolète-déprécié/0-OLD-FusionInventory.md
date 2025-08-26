# FusionInventory

**FusionInventory** = système **client-serveur** utilisé pour faire de l’**inventaire automatique** des équipements dans un réseau.

Deux composants : 
- **Le plugin côté serveur GLPI** (plugin FusionInventory). 
- **L'agent FusionInventory**, installé sur les machines clientes à inventorier (appelé "agent").

L'agent peut être installé sur : **Windows, Linux, macOS, Android, etc.** L’agent est écrit en **Perl**, donc **portable** sur plusieurs OS.



## **🔗 Communication agent ↔ serveur**

- L’**agent collecte** des données (matériel, OS, uptime, etc.).
- Il les **envoie au serveur GLPI** via une **URL** (vers le plugin FusionInventory).
- La **communication se fait en HTTP/HTTPS**, données au **format XML**.
- L’agent peut aussi **écouter sur un port** (mini-serveur web intégré) → permet au serveur GLPI d’envoyer des requêtes à l’agent (ex: déploiement logiciel).



## **📦 Fonctions principales de FusionInventory**

1.  **Inventaire matériel et logiciel**

    - **OS, RAM, CPU, disques, logiciels installés, uptime, etc.**
    - **Infos envoyées au plugin Fusion dans GLPI.**

2.  **Déploiement de logiciels** 💡

    - **Mode Push (depuis le serveur) ou Pull (l'agent vient chercher les tâches).**
    - **Possible de planifier le déploiement hors horaires via Wake-on-LAN (WOL).**

3.  **Collecte d’infos spécifiques** (non standard)

    - **Exemple : uptime d’un serveur via la base de registre sous Windows ou en ligne de commande.**

4.  **Découverte réseau (Network Discovery)**

    - **Détection d’adresses IP actives.**
    - **Peut scanner un réseau pour découvrir de nouvelles machines.**

5.  **Inventaire sans agent** grâce à **SNMP**

    - **Pour imprimantes, switches, routeurs, etc.**
    - **L’agent agit alors comme une passerelle SNMP et interroge ces équipements.**

6.  **Wake-on-LAN (WOL)**

    - **Permet d’allumer à distance une machine via un paquet magique réseau.**
    - **Utilisé pour faire des actions en dehors des horaires utilisateurs (ex: déploiement logiciel la nuit).**



### **⚙️ Règles de gestion dans GLPI**

Utilisées pour **organiser les données remontées** par FusionInventory.

1.  **Règles d'affectation** :
    - Par **adresse IP** → Si IP = 192.168.10.X → machine du site de Tours
    - Par **tag (balise)** défini dans l’agent → sert à identifier l'entité, lieu, ou l'user.
2.  **Règles de filtrage** :
    - éviter d’importer **certains logiciels** (patchs, vieux logiciels, etc.).
    - Géré via les **dictionnaires** : listes d’exclusion ou de fusion.
3.  **Règles d’import / fusion d’équipements** :
    - Pour éviter les doublons (deux fois la même machine).
    - Mise à jour de machines existantes plutôt que création de doublons.
4.  **Règles sur les entités / lieux / utilisateurs :** Affectation automatique à une entité GLPI selon les données de l’agent.
5.  **Liste des équipements ignorés :** Évite de traiter certaines machines ou matériels spécifiques (ex: équipements trop anciens ou non pertinents).

