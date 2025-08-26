# Gestion de la sécurité

## **🔐 Les 5 piliers de la sécurité des données (DICAN)**

| **🔠 Terme** | **🧠 Définition claire** | **🔧 Exemple concret** |
|----|----|----|
| **D** – Disponibilité | L’utilisateur doit pouvoir **accéder aux données quand il en a besoin**. | RAID 1, RAID 5, redondance, serveurs de secours, onduleurs... |
| **I** – Intégrité | Les données doivent être **exactes et non altérées**. | Vérification avec des **hashes (MD5, SHA256)** sur les ISO Linux |
| **C** – Confidentialité | Seules les **personnes autorisées** peuvent accéder à l’info. | Permissions NTFS sous Windows, ACL, chiffrement |
| **A** – Authenticité | Il faut pouvoir **vérifier l’identité** de l’émetteur des données. | Certificats numériques, signature électronique |
| **N** – Non-répudiation | L’auteur d’une action **ne peut pas nier** l’avoir faite. | Signature numérique dans un mail : prouve que c’est bien toi |



## **🧪 Détails et illustrations**

### **📦 Disponibilité**

- L’info doit être accessible **à tout moment**.
- Techniques : RAID (Redondance de disques), sauvegardes, réplication, tolérance de panne, monitoring, supervision.



### **🔍 Confidentialité**

- On protège les données des **accès non autorisés**.
- Outils :
  - **Permissions NTFS** : Lecture seule, modification, contrôle total.
  - **Chiffrement** (AES, RSA...) pour empêcher la lecture en clair.
  - Authentification forte (MFA).



### **🧾 Authenticité**

- Vérifie l’**identité du créateur** ou de la source d’une donnée.
- Utilise des **certificats numériques**, parfois combinés à des clés publiques/privées.
- Exemple : HTTPS garantit que le site est bien le bon (via certificat SSL/TLS).



### **📏 Intégrité**

- Garantit que les données **n'ont pas été modifiées** ou corrompues.
- Exemple : Téléchargement d’un ISO Linux → on compare le hash (MD5/SHA256) du fichier téléchargé avec celui fourni par le site officiel.



### **✉️ Non-répudiation**

- Empêche une personne de **nier** qu’elle a effectué une action.
- Exemple : un mail signé électroniquement → tu ne peux pas dire ensuite que ce n’est pas toi qui l’as envoyé.
- Permet une **traçabilité** fiable.

