# Hygiène informatique**🧼 Qu'est-ce que l'hygiène informatique ?**

C’est l’ensemble des **bonnes pratiques de base** pour garantir un **niveau de sécurité minimum** dans les SI.

➡️ Objectifs : protéger les données, gérer les accès, éviter les erreurs humaines ou techniques.

➡️ C’est **préventif** : on agit **en amont** pour limiter les incidents.



**🏛️ Rôle de l’ANSSI**

L’**ANSSI** (Agence nationale de la sécurité des systèmes d'information) est un acteur majeur 🇫🇷 créé en 2009.

Elle publie des guides, dont le célèbre **Guide d’hygiène informatique** 🧾

➡️ Ce guide contient **42 mesures concrètes** pour améliorer la sécurité des SI.

➡️ Il aide à structurer une démarche **progressive**, **réaliste**, et **priorisée**.



**📘 Importance du Guide d’hygiène**

Outil pédagogique + opérationnel.

➡️ Il aide à **comprendre les risques**, **identifier les besoins**, et **mettre en place les protections adéquates**.

➡️ Construit sur une logique **d’amélioration continue**.



**🧾 Politique de sécurité des SI (PSSI)**

La PSSI est le **document stratégique de référence**.

➡️ Elle est rédigée par la **DSI** et/ou le **RSI**, et formalise les **règles, principes et procédures** de sécurité à suivre.



**🛠️ Élaboration de la PSSI**

Elle entraîne la rédaction de plusieurs documents techniques, notamment pour le **filtrage réseau des pare-feux** 🔥

➡️ Ces règles sont organisées en **sections**.



**🧱 Sections d'une politique de filtrage réseau**

1.  **Autorisation entrante** : ce qui est autorisé vers le pare-feu
2.  **Autorisation sortante** : ce que le pare-feu peut émettre
3.  **Protection du pare-feu** : limitation des attaques directes
4.  **Flux métiers** : règles spécifiques aux applis métiers
5.  **Antiparasites** : bloquer les flux inutiles/noise
6.  **Interdiction finale** : tout ce qui n’est pas explicitement autorisé est bloqué



**🏷️ Conventions de nommage**

Les conventions permettent une **lecture claire et homogène** des règles.

Deux approches possibles : ➡️ **Fonctionnelle** : ex. SRV_DNS_INTERNE ➡️ **Technique** : ex. SRV_Apollo_TCP_21000
