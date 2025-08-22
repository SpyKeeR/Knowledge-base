# **🗂 Plage et Fonctionnement des Adresses APIPA**

| **Type** | **Plage d'adresses** | **Masque de sous-réseau** | **Routable sur Internet ?** |
|----|----|----|----|
| **APIPA** | 169.254.0.0 - 169.254.255.255 | 255.255.0.0 | ❌ Non |


## 🔹 **Comment ça marche ?**

1️⃣ **L’appareil demande une adresse IP** au serveur DHCP.

2️⃣ **Pas de réponse ?** → Il génère une adresse dans la plage **169.254.x.x**.

3️⃣ **Vérification avec ARP** → Il envoie une requête ARP pour voir si l’adresse est déjà utilisée.

4️⃣ **Si pas de réponse** → Il garde l’adresse.

5️⃣ **Si l’adresse est déjà prise** → Il en génère une autre et recommence.



## **🔍 Pourquoi APIPA est-il utile ?**

✔ **Communication locale maintenue** → Même sans DHCP, les appareils peuvent **échanger des fichiers et imprimer**.

✔ **Configuration automatique** → Aucun besoin d'intervention manuelle.

✔ **Pratique pour les petits réseaux** → Surtout dans les bureaux ou maisons sans serveur DHCP permanent.

### ⚠ **Limites d’APIPA** :

❌ **Pas d’Internet** → Les adresses **ne sont pas routables**.

❌ **Ne fonctionne que sur un même sous-réseau** (169.254.x.x).



## **🏠 Exemple Pratique : Panne DHCP dans un Bureau**

### 📌 **Situation** :

- Les PC sont configurés en **DHCP**.
- Le **serveur DHCP tombe en panne**.
- Chaque PC obtient une **adresse APIPA (ex: 169.254.12.34)**.

### 📌 **Conséquences** :

✔ **Les PC du même réseau peuvent toujours communiquer.**

❌ **Pas d’accès Internet ni à d’autres réseaux.**

🔄 **Dès que le serveur DHCP revient, les appareils libèrent leur adresse APIPA et reprennent une IP normale**.
