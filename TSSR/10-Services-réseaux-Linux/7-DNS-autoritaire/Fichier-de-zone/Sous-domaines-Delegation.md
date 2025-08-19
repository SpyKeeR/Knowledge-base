# Sous-domaines - Delegation**🌱 Sous-domaines (sans délégation)**

Un **sous-domaine** (ex : cdb.eni-ecole.bzh) peut être **géré directement dans la zone du domaine parent** sans aucune modification du serveur DNS.

👀 Points clés :

- La **hiérarchie DNS** permet de définir des noms complets, même pour des sous-domaines, dans le **même fichier de zone**.
- Pas besoin de config serveur supplémentaire.
- L’entrée se fait simplement par nom (ex : [www.cdb](http://www.cdb) ou rds.nrt) dans db.eni-ecole.bzh.
- $ORIGIN définit la base pour les enregistrements relatifs (ici : eni-ecole.bzh.).
- Le nom complet est déduit (ex : [www.cdb](http://www.cdb) devient [www.cdb.eni-ecole.bzh](http://www.cdb.eni-ecole.bzh).).

📌 Exemple :

- [www.cdb](http://www.cdb) A 35.12.13.16 → géré dans la zone parente
- Pas de séparation logique ou d’autorité → **centralisé mais simple**



**🧭 Délégation DNS : concept**

La **délégation** sert à **confier la gestion d’un sous-domaine à un autre serveur DNS**. On conserve la supervision dans la zone parente, mais la gestion passe ailleurs.

🎯 Objectif :

- Définir **jusqu’où s’étend l’autorité** de la zone parente
- Permettre à un **autre serveur DNS** de gérer les enregistrements du sous-domaine
- Utile en entreprise pour séparer corp.mondomaine.fr, infra.mondomaine.fr, etc.



**🧰 Mise en œuvre d'une délégation**

Dans la **zone parente**, on ajoute :

- NS du sous-domaine pointant vers le serveur autoritaire de la zone déléguée (ex : NS nsb.prism.lcl. pour fiveeye.prism.lcl.)
- **Glue record** obligatoire si le serveur NS fait partie du domaine délégué (ex : nsb A 10.2.0.53)

🧠 Exemple (dans db.prism.lcl) :

- fiveeye.prism.lcl. NS nsb.prism.lcl.
- nsb A 10.2.0.53 ➜ permet de **résoudre le serveur NS** même sans accès à la zone déléguée



**🔄 Serveur résolveur : adaptation nécessaire**

Quand le serveur DNS est **résolveur en plus d’être autoritaire**, il faut **désactiver les redirecteurs globaux** (si configurés) pour éviter les confusions.

📎 Exemple Bind9 :

- Dans le bloc zone "prism.lcl", ajouter forwarders {}; pour **forcer la résolution locale** du sous-domaine sans passer par les redirecteurs définis dans named.conf.options.
