## **🧑‍💻 1. Adresses Unicast**

- **Utilisation :** Identifie un **unique appareil** sur un réseau.
- **Exemple :** Adresse d'une machine spécifique.



## **📢 2. Adresses Multicast**

- **Utilisation :** Envoie des paquets à plusieurs appareils en **une seule transmission**, remplaçant les diffusions (broadcast) de l'IPv4.
- **Exemple :** Utilisé pour des groupes de diffusion.



## **🔄 3. Adresses Anycast**

- **Utilisation :** Envoie des paquets au nœud le plus proche parmi plusieurs hôtes partageant la même adresse.
- **Important :** La plage **FD00::/8** peut être utilisée aussi bien pour **Anycast** que pour **Unicast**, selon l'annonce de l'hôte au routeur et l'objectif de l'adresse.



## **🌐 Adresses IPv6 Spécifiques**

- **::/0 > Signification :** Représente **tous les réseaux**, utilisée comme **route par défaut** (équivalent de 0.0.0.0/0 en IPv4).
- **::/128 > Signification : Adresse non spécifiée**, utilisée avant l’auto-configuration d’une adresse lien-local.
- **::1/128 > Signification : Adresse de boucle locale** (équivalente de 127.0.0.1 en IPv4).
- **FE80::/10 > Signification : Adresse de lien-local** pour la communication sans passer par un routeur (similaire à l’APIPA en IPv4).
- **FC00::/8 > Signification : Adresses uniques locales (ULA)** non routables sur Internet, similaires aux **IPv4 privées**.
  - **Important : FC00::/8 n'est plus utilisée**, et elle est maintenant remplacée par **FD00::/8** pour les adresses uniques locales.
- **2000::/3 > Signification : Adresses globales** routables sur Internet.
- **FF00::/8 > Signification : Scope de tous les types de multicast**.
  - **FF01::/16 > Signification : Multicast Interface-Local**, pour la diffusion à l’intérieur d’une interface.
    - **Caractéristique : Non routable**, limité au nœud (Pour des tests locaux).
  - **FF02::/16 > Signification : Multicast Link Local**, pour la diffusion à l’intérieur d’un lien local.
    - **Caractéristique : Non routable, ne peut pas passer un routeur**.
  - **FF05::/16 > Signification : Multicast Site Local**, pour la diffusion à l'intérieur d'un site.
    - **Caractéristique : Routable en interne**, délimité a un sous-réseau.
  - **FF08::/16 > Signification : Multicast Organization Local**, pour la diffusion à l'intérieur de plusieurs sites.
    - **Caractéristique : Routable en interne**, délimité à plusieurs sous-réseaux (via routage multicast).
  - **FF0E::/16 > Signification : Multicast Global**, pour la diffusion sur internet.
    - **Caractéristique : Routable**, très peu utilisé car peu de support des opérateurs.
