# 7 - DNS autoritaire**🧭 Objectifs du module Serveur DNS autoritaire**



**📋 Découvrir les différents enregistrements (records)**

Tu vas apprendre à gérer les types principaux :

- **A** (adresse IPv4),
- **AAAA** (IPv6),
- **MX** (serveur mail),
- **CNAME** (alias),
- **NS** (serveur de noms),
- **SOA** (Start Of Authority),
- **PTR** (reverse DNS),
- **TXT** (infos diverses, SPF...).

🎯 Savoir quoi utiliser, pourquoi, et comment le formater.



**⚙️ Mettre en place une config DNS pour gérer une zone**

- Déclarer une **zone DNS** dans la config de bind9,
- Créer le **fichier de zone** contenant les enregistrements (avec la syntaxe BIND),
- Définir les **droits**, **permissions** et **répertoires** utilisés,
- Tester ta config avec named-checkconf, named-checkzone, dig, nslookup.



**🧬 Mettre en place une zone primaire et secondaire**

- **Zone primaire** : là où tu **édites** les enregistrements, c’est le **maître**.
- **Zone secondaire** : copie **lecture seule**, appelée **esclave**, elle fait des **transferts de zone (AXFR)** pour rester synchro.

🔁 Objectif : **répartition de charge**, **sécurité**, **disponibilité**.
