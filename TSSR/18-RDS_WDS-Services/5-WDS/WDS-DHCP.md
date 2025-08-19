# WDS / DHCPLorsqu’un poste client démarre en **mode PXE**, il envoie une requête réseau pour :

- Obtenir une **adresse IP** et les paramètres réseau
- Savoir **où trouver** le fichier d’amorçage (boot) pour se lancer

| **Rôle**              | **Serveur DHCP**    | **Serveur WDS**        |
|-----------------------|---------------------|------------------------|
| IP (bail, masque, GW) | ✅                  | ❌                     |
| Options PXE (66/67)   | ✅ *(si configuré)* | ✅ *(mode Proxy DHCP)* |



**🧭 Deux modes de fonctionnement**

**🧩 A. DHCP et WDS sur le même serveur (intégré)**

- Le **même DHCPOFFER** contient à la fois :
  - Les infos d’adressage IP
  - Les **options PXE** :
    - Option 66 → Nom ou IP du serveur TFTP (ex. : 192.168.1.1)
    - Option 67 → Nom du fichier de boot (ex. : boot\x64\wdsnbp.com)
- Le client répond à **ce seul DHCPOFFER**, puis envoie un **DHCPREQUEST**, et reçoit un **DHCPACK**.
- **Aucune intervention de WDS en mode proxy DHCP** : tout passe par DHCP.



**🧩 B. DHCP et WDS sur des serveurs distincts (mode Proxy DHCP activé sur WDS)**

- Le **serveur DHCP** répond avec :
  - Le bail IP
  - (éventuellement) les options PXE
- **Le serveur WDS (en mode Proxy DHCP)** peut aussi écouter la requête PXE (DISCOVER) et répondre séparément avec :
  - Un message **PXE-only** (pas un DHCPOFFER)
  - Contenant les options 66 (TFTP) et 67 (fichier de boot)
- Le client choisit **un seul bail IP** via DHCPREQUEST/ACK, mais utilise **les infos PXE** du WDS pour contacter le bon fichier d’amorçage.

🛑 Le client **ne mixe jamais deux offres DHCP**. Il choisit **un seul serveur DHCP** pour son bail IP.



**📝 Résumé final**

✅ Pour que WDS fonctionne avec PXE :

- Le client a **obligatoirement besoin d’une IP DHCP**
- Il a aussi besoin des **informations PXE** pour récupérer le fichier de démarrage
- Ces infos peuvent venir : Soit du **serveur DHCP** (avec options 66/67) - Soit du **serveur WDS** en mode Proxy DHCP

🔧 Le tout dépend de l'architecture mise en place :

- **DHCP + WDS sur le même serveur** → tout est intégré dans une seule réponse DHCP
- **DHCP et WDS séparés** → WDS complète la réponse via un message ProxyDHCP

**🚀 Processus PXE résumé**

1️⃣ Le client envoie un **DHCPDISCOVER**

2️⃣ Le serveur DHCP répond avec un **DHCPOFFER** (IP + options)

3️⃣ Si WDS est séparé, il répond aussi en **Proxy DHCP** (options PXE uniquement)

4️⃣ Le client envoie un **DHCPREQUEST** vers le serveur DHCP choisi

5️⃣ Le serveur DHCP confirme avec un **DHCPACK**

6️⃣ Le client contacte le **serveur TFTP** via l’option 66

7️⃣ Il télécharge le **fichier de boot** spécifié en option 67

8️⃣ L’installation démarre depuis l’image boot (boot.wim)
