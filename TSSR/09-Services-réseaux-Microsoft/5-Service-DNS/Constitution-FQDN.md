# Constitution FQDN**🌍 Qu’est-ce qu’un FQDN ? (Fully Qualified Domain Name)**

Le **FQDN** est le **nom complet et unique** d’un hôte dans un **espace de nom DNS**.

Il identifie **précisément une machine** sur un réseau (interne ou Internet) grâce à un nom structuré :

➡️ **NomHôte.NomDomaine.TLD.** (ex : srv1.infra.local.)

🎯 **À retenir** :

- Partie **hôte** = nom de la machine ou du service (ex : srv1)
- Partie **domaine** = organisation ou sous-réseau logique (ex : infra.local)
- Terminaison par un **point implicite** pour la racine (.)



**🧾 Différence NetBIOS vs FQDN**

- **Nom NetBIOS** : nom court, local, non hiérarchique (ex : SRV1)
- **FQDN** : nom complet DNS, structuré et résoluble à l’échelle globale ou locale (ex : srv1.infra.local)

➡️ Dès qu’une machine est **intégrée à un domaine**, son FQDN devient la **référence réseau** utilisée pour la résolution DNS.



**🧠 Exemple de FQDN avec [www.google.fr](http://www.google.fr)**

Dans [www.google.fr](http://www.google.fr) :

- www = **nom de l’hôte** (souvent un serveur web)
- google = **domaine de l’organisation**
- fr = **TLD** (Top-Level Domain) correspondant au pays 🌍  
  🔹 Les points sont **des séparateurs hiérarchiques**, de gauche à droite, du plus spécifique au plus général.



**⚠️ Ne pas confondre : domaine DNS vs domaine AD**

- **Domaine DNS** : utilisé pour **la résolution de noms** via le protocole DNS
- **Domaine Active Directory (AD)** : utilisé pour **l’authentification** et la **gestion des objets** dans un annuaire LDAP

Même s’ils peuvent **porter le même nom**, ils sont **deux entités différentes**, avec des fonctions séparées.



**🧱 Espaces de noms : public vs privé**

- 🌐 **Public** : visible sur Internet (ex : entreprise.com)
- 🏢 **Privé** : utilisé en interne (ex : corp.local, lan.infra)  
  ⚠️ **Éviter d’utiliser des noms déjà enregistrés sur Internet** pour éviter les conflits DNS.

💡 Astuce : pour les usages privés, privilégier des **noms non référencés publiquement** (.lan, .local, .priv...) ou créer une zone interne bien définie.
