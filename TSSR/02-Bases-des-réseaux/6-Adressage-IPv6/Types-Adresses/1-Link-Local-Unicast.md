# Link-Local Unicast

Une adresse de monodiffusion de lien local est utilisée pour la communication entre dispositifs sur un même réseau local sans routeur.


## **🧑‍💻 Caractéristiques**

- **Plage d'Adresse :** FE80::/10 (limitée au lien local, non routable) (Communément segmentés en /64)
- **Format de l'Adresse :**
  - **EUI-64 avec Adresse MAC** : Génération à partir de l'adresse MAC depuis l'hôte
  - **SLAAC Génération aléatoire de l'IID** : Amélioration de la confidentialité (Sans interaction au routeur)
  - **Adresse Fournie par un Serveur DHCPv6** : Attribuée via DHCPv6 (Rare, car DHCPv6 est plus utilisé pour des globales/locales uniques)
  - **Génération à partir d'un Identifiant Unique** : Numéro de série, identifiant matériel (Statique, via un administrateur)
- **Pas de Routage** : Communication limitée au réseau local
- **Fonctionnement Automatique** : Pas besoin de serveur DHCP
- **Exemples** :
  - FE80::1 (un seul appareil)
  - FE80::a00:27ff:feeb:1234 (dérivée de l'adresse MAC)

## **🧑‍💻 Rôle dans l'Attribution d'une Adresse Globale**

- **SLAAC** :

  1.  Génération d'une adresse de lien local
  2.  Envoi d'une requête ICMPv6 (NDP) pour découvrir un routeur
  3.  Réception d'un préfixe global pour configurer une adresse IPv6 globale

- **Communication avec un Routeur** :
  - L'adresse de lien local permet d'échanger avec le routeur avant l'attribution d'une adresse globale routable.



## **🧑‍💻 Importance des Adresses de Lien Local**

- **Indépendance des serveurs DHCP** : Démarrage sans serveur DHCP, utile pour les configurations temporaires
- **Communication initiale** : Essentielle avant l'attribution d'une adresse globale
- **Protocole Neighbor Discovery (NDP)** : Gestion des adresses sur le même lien, résolution IP ↔ MAC
