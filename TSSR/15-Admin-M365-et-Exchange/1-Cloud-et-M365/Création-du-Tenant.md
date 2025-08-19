# Création du Tenant🏗️ **Création initiale du Tenant**

1️⃣ Choisir un **plan Microsoft 365** adapté aux besoins

2️⃣ Fournir une **adresse mail valide** pour l’inscription

3️⃣ Sélectionner un **essai gratuit** de 30 jours (souvent utilisé en test)

4️⃣ Renseigner les **informations de l’entreprise**

5️⃣ **Valider** le tout pour finaliser la création du tenant



🆔 **Nom du Tenant et domaine .onmicrosoft.com**

🔤 Un nom de tenant de type nomchoisi.onmicrosoft.com est attribué

🟡 Ce nom est **unique** et **non modifiable** une fois défini

📤 Ce domaine est **routable** : on peut envoyer et recevoir des mails

⚠️ Ce nom n’est **pas professionnel** → préférer un domaine personnalisé



🌐 **Personnalisation avec un domaine professionnel**

📬 Pour une adresse type @entreprise.fr, il faut :

- Acheter/louer un **nom de domaine** auprès d’un **registrar**
- Exemples de registrars : **OVH**, **Gandi**, **GoDaddy**, **AFNIC  **
  🔎 Vérifier la **disponibilité de domaines personnalisés** (ex : prénomnom.com)  
  💸 Comparer les **prix selon les extensions** (.fr, .com, .net…)



📡 **Configuration des enregistrements DNS pour Exchange Online**

📨 Ajouter un enregistrement **MX** :

- Requis pour la **réception des mails**

🔁 Ajouter un enregistrement **CNAME** :

- Requis pour **l’autodiscovery** (configuration automatique des clients)

🔐 Ajouter un enregistrement **SPF** (type TXT) :

- Ex : v=spf1 include:spf.protection.outlook.com -all
- Garantit que seul **Microsoft 365 est autorisé à envoyer des mails** pour ce domaine

🧹 **Nettoyer la zone DNS** :

- Supprimer les entrées inutiles
- Garder uniquement les enregistrements **MX**, **CNAME** et **SPF**

⚙️ **Ajout du domaine personnalisé dans Microsoft 365**

📍 Depuis le **Centre d’administration** Microsoft 365 :

1️⃣ Aller dans **Domaines**

2️⃣ Cliquer sur **+ Ajouter un domaine**

3️⃣ Saisir le **nom du domaine acheté**

🔐 Ajouter un **enregistrement TXT** dans la zone DNS publique du domaine :

TXT MS=MS=xxxxxxxxxxxx

✅ Cela permet à Microsoft de **vérifier la propriété du domaine**
