# Composants clés🔐 **Authentification unique (SSO - Single Sign-On)**

• Permet une connexion unique en début de journée, évitant la saisie répétée des identifiants

• Configuration via stratégie de domaine pour éviter la double authentification

• Paramètre important : **Autoriser la délégation d’informations d’identification** par défaut

• Offre une expérience fluide et sécurisée aux utilisateurs



📜 **Gestion des licences RDS**

• Nécessité d’acquérir des **Licences d’Accès Client (CAL)** pour permettre l’accès au service RDS

• Utilisation d’un **serveur de licences RDS** dédié qui attribue une licence à chaque connexion utilisateur

• Si les licences sont épuisées, plus aucune connexion n’est possible

• Période d’utilisation sans licence possible : **120 jours en mode d’évaluation** (dans un cadre d’apprentissage)



🌐 **Passerelle de services de bureau à distance (RD Gateway)**

• Facilite l’accès sécurisé à l’infrastructure RDS depuis l’extérieur via **le protocole HTTPS**

• Rôle complémentaire, souvent installé sur un serveur dédié

• Essentiel pour les utilisateurs distants (ex : commerciaux en déplacement)

• Usage parfois remplacé ou complété par les VPN



🎛️ **Service Broker pour les connexions RDS**

• Gère et distribue les demandes de connexion entre plusieurs serveurs RDS

• Assure l’**équilibrage de charge** entre serveurs en cas de forte demande

• Permet la **reconnexion d’un utilisateur à sa session existante** après coupure sans perte de données

• Fonctionne comme un chef d’orchestre des sessions utilisateurs
