# Transferts & Enr. dynam.**🧾 Enregistrements DNS : statiques vs dynamiques**

Le **DNS** relie des **noms d'hôtes ↔️ adresses IP**.

Ces enregistrements peuvent être :

- **🔒 Statiques** : saisis à la main (ex : MX, A, CNAME pour mail, web...) → **fiables, figés**
- **♻️ Dynamiques** : créés automatiquement (ex : un poste rejoint un **AD** et s’enregistre dans DNS, avec A et PTR) → **souples, adaptés aux IPs volatiles**

💡 *Pré-requis pour les dynamiques* : zone DNS configurée en **mise à jour dynamique** (sécurisée de préférence).



**🔄 Transferts de zone DNS**

Quand on a un **serveur maître (primaire)** et un ou plusieurs **serveurs esclaves (secondaires)**, il faut **synchroniser les zones**.

➡️ Le but : assurer la **redondance et la cohérence**.

Voici le déroulé 📤📥 :

- 🧭 **Le secondaire interroge** le maître : numéro de série changé ?
- ✅ Si oui, il lance un **transfert de zone** :
  - **IXFR (Incremental Zone Transfer)** : récupère *seulement les modifs*
  - **AXFR (Full Zone Transfer)** : récupère *toute la zone*
- 🔔 Le maître peut **notifier** les secondaires après modif → plus réactif



**📌 Rappel sur le numéro de série**

Dans l’enregistrement **SOA**, on trouve un **numéro de série**. À **chaque modif**, il doit être **incrémenté**.

C’est ce qui permet aux secondaires de savoir s’ils doivent se mettre à jour 🔁
