# Remédiation Attaques🧠 **IDS (Intrusion Detection System) >** Système de **détection passive** des intrusions. 🔎 Idéal pour détecter les attaques sans impacter les flux.

• 📡 Analyse le trafic réseau à la recherche de **signatures d'attaques connues** ou de comportements anormaux.

• ⚠️ Envoie des **alertes** lorsqu’une activité suspecte est détectée (ex. : scan de ports, tentative d’exfiltration).

• ❌ **Ne bloque pas** le trafic automatiquement : il est souvent utilisé pour **auditer** et **surveiller**.

• 👀 Doit être **couplé à une intervention humaine ou automatisée** pour une réaction.

🚫 **IPS (Intrusion Prevention System) >** Système de **détection ET de blocage actif**. 🔧 Utilisé sur les points critiques du réseau (entre WAN/LAN, DMZ, etc.).

• 🔐 Placé en **inline**, c’est-à-dire directement dans le flux réseau.

• ⚙️ Analyse en temps réel les paquets pour **bloquer immédiatement** les connexions suspectes.

• 📉 Permet de **réduire considérablement le risque** de compromission s’il est bien configuré.

• ❗ Peut parfois générer des faux positifs, donc nécessite un bon calibrage.

🛠️ **EDR (Endpoint Detection & Response) >** Surveillance active des **postes de travail** et **serveurs** (endpoints). 🎯 Clé pour lutter contre les menaces internes et les attaques qui échappent aux antivirus classiques.

• 👁️‍🗨️ Suit les **comportements users** et des processus en temps réel. • 🔥 Détecte les menaces avancées (ransomwares, malwares furtifs, attaques sans fichiers).

• 🧯 Permet d’**isoler un poste compromis**, de le **nettoyer** et de **remonter les causes** de l’incident.

• 📈 Utilise souvent l’intelligence artificielle pour reconnaître des **patterns de compromission**.

🌐 **XDR (Extended Detection & Response) >** Extension de l'EDR à l'échelle **multi-environnement**. 🌐 Adapté aux infrastructures hybrides et aux grandes organisations.

• 🌍 Corrèle les données issues des endpoints, **réseau, messagerie, cloud, SIEM, etc.**

• 🧩 Fournit une **vision globale** de la menace : ce qui aurait été invisible en silo devient lisible.

• 🧠 Identifie des **campagnes d'attaques coordonnées** ou persistantes.

• 🔄 Permet une **automatisation plus fine** des réponses (ex. : blocage simultané d’un compte + poste + boîte mail).

👥 **MDR (Managed Detection & Response) >** Service de **détection et réponse managé** par un prestataire externe. 🔐 Gain de temps, de réactivité et de sérénité.

• 🧑‍💻 L’entreprise délègue la surveillance à une **équipe de cybersécurité dédiée (SOC)**.

• ⏱️ Analyse et **prise en charge 24/7** des alertes générées par les outils internes (EDR, SIEM, etc.).

• 📊 Fournit des **rapports réguliers** et des conseils d'amélioration.

• 💼 Intéressant pour les **PME** ou structures sans ressources internes suffisantes.

🕸️ **ZTNA (Zero Trust Network Access) >** Modèle de sécurité basé sur le principe **“ne jamais faire confiance, toujours vérifier”**. 🔒 Permet de **limiter fortement la propagation** d'une attaque si un compte est compromis.

• 🧑‍💻 Chaque demande d’accès est vérifiée selon l’**identité, le poste, la localisation, l’heure**, etc.

• 🔐 Remplace les approches classiques de type VPN en **n'accordant que le minimum de privilèges nécessaires**.

• 🧱 Segmentation extrême : même un collaborateur authentifié ne peut accéder qu'à ce qui lui est autorisé.

• 📡 Idéal en télétravail, cloud, ou contexte multi-sites.

🧱 **Bastion (Jump Server) >** Serveur intermédiaire pour accéder aux systèmes critiques en toute sécurité. 🎥 Utilisé notamment dans les environnements où des prestataires externes accèdent aux serveurs.

• 🛑 Joue un rôle de **filtrage et d’audit** : tous les accès passent par lui. • 🔐 Exige une **authentification renforcée** (MFA, certificats, etc.).

• 📝 **Journalise** toutes les actions réalisées (commandes, fichiers transférés, sessions ouvertes). • 📛 Peut restreindre les actions possibles selon le rôle de l’utilisateur.
