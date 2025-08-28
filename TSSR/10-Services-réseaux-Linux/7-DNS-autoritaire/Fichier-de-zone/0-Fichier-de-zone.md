# Fichier de zone

## **🧾 Fichier de zone DNS : structure générale (placé dans /var/cache/bind, défini dans named.conf.options)**

Le **fichier de zone** contient **toutes les données d’un domaine** dans un serveur DNS (Bind). Il est divisé en **trois grandes parties** :

- **Directives globales** ($ORIGIN, $TTL) 
- **En-tête SOA et enregistrements NS**
- **Enregistrements de ressource** (A, AAAA, MX, CNAME, PTR, etc.)

![](../../../media/Cours-Services-réseaux-Linux-Fichier-de-zone-image1.png)

### **🧷 Partie 1 – Les directives globales**

- $ORIGIN 🌐 : définit le **nom du domaine principal** de la zone (ex: unite-ecole.bzdesas.).
- $TTL ⏳ : valeur par défaut du **Time To Live** (en secondes), ex : 86400 (24h). Ça indique **combien de temps** un enregistrement peut être mis en cache.

🔎 Ces deux directives sont valables pour **tous les enregistrements** qui suivent.



### **📌 Partie 2 – Le SOA (Start of Authority) & NS**

Cette section est **critique**, c’est le cœur de la zone. Elle commence toujours par un enregistrement **SOA** :

- @ = alias de $ORIGIN
- **Serveur DNS maître** 🧠 : nom FQDN du serveur autoritaire primaire (terminé par un point).
- **Adresse mail admin** 📧 : admin.unite-ecole.bzdesas. devient admin@unite-ecole.bzdesas.

| Serial | 🔁 Numéro de version de la zone. Format recommandé : AAAAMMJJxx. |
|--|--|
| Refresh | 🔄 Fréquence de MAJ de la zone par les serveurs secondaires (ex : 86400 = 24h). |
| Retry | ⏱️ Si échec de MAJ, intervalle de nouvelle tentative (ex : 7200 = 2h). |
| Expire | 💀 Durée max pendant laquelle un secondaire garde les infos sans contact avec le primaire. |
| TTL nég. | ⛔ Durée de cache pour les réponses négatives (ex : 86400 = 24h). |

Juste après le SOA, tu déclares les **NS** (serveurs DNS autoritaires) : Ex : @ NS dns1.unite-ecole.bzdesas.



### **📍 Partie 3 – Les enregistrements de ressources (RRTYPE)**

Voici les enregistrements qu'on retrouve dans la base de données DNS 🗂️ :

- **A** 🌍 : nom de domaine vers IPv4 (www IN A 44.0.0.80)
- **AAAA** 🧪 : nom vers IPv6 (non présent dans l’exemple, mais équivalent du A)
- **CNAME** 🔗 : alias vers un autre nom (shop CNAME www)
- **MX** 📬 : serveurs de mails (@ MX 10 smtp.mailhost.net. → priorité 10, serveur SMTP)
- **PTR** 🔁 : zone inverse, associe une IP à un nom.


## **🔄 Zone inverse (Reverse DNS)**

Une zone inverse est définie dans un fichier à part pour faire la **résolution IP → nom**. Elle utilise des **PTR** :

Ex : IP 192.168.1.2 devient : 2.1.168.192.in-addr.arpa. > Enregistrement : 2 IN PTR srv01.unite-ecole.bzdesas.





