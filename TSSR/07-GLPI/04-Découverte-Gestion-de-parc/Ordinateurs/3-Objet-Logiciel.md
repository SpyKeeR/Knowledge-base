# Logiciels dans GLPI

## 🧠 Notion clé
- Un **logiciel** est un élément **immatériel** (contrairement aux matériels comme PC, imprimantes, etc.).  
- Il peut être **installé sur plusieurs postes** et est souvent classé par :  
  - **Catégorie** (ex. Bureautique, Graphisme, Sécurité)  
  - **Nom** (ex. Microsoft Office)  
  - **Version** (ex. Office 365 Pro Plus, Office 2021)  
  - **Statut** : installé ou en stock (non utilisé)

---

## 🗂️ Étapes de gestion d’un logiciel

### 1️⃣ Créer des catégories logicielles
Exemples : Bureautique / Graphisme / Sécurité  
👉 Permet d’**organiser** et **filtrer** les logiciels facilement.

### 2️⃣ Créer un logiciel
Menu : **Accueil > Parc > Logiciels > + Ajouter**  
Deux options :  
- **📄 Gabarit vide** → tout à remplir soi-même  
- **📑 Gabarit existant** → base pré-remplie à ajuster  

Infos à renseigner :  
- **Nom** (ex. Microsoft Office 2021)  
- **Éditeur** (ex. Microsoft)  
- **Catégorie** (Bureautique, Système…)  
- **Lieu** (souvent "stock")  
- **Technicien & Groupe responsable**  
- **Utilisateur** (si licence nominative, souvent vide)  
- **Image** (facultative, utile visuellement)  
- Définir si le logiciel peut être **associé à des tickets** (pour centraliser les incidents liés).  

⚠️ Important : un objet logiciel n’est **jamais affecté directement à un poste** → il est lié via **versions** et **installations**.

### 3️⃣ Créer une version du logiciel
Exemples : Office 2016 / 2019 / 2021 ou Office 365 Pro Plus  
👉 On peut préciser :  
- Compatibilité OS  
- Autres caractéristiques utiles  

### 4️⃣ Associer une licence à une version
Menu : **Accueil > Gestion > Licences > + Ajouter**  
Une licence est un objet à part entière, toujours **liée à un logiciel et une version**.  

Infos à remplir :  
- **Logiciel associé** / **Nom de la licence** (ex. Office 2021 Pro Volume)  
- **Type de licence** :  
  - **Boîte** : physique  
  - **Volume** : en lot, multi-postes  
  - **OEM** : liée à une machine, préinstallée  
  - **Libre** : ex. LibreOffice  
- **Numéro de série / Nombre de licences / Version / Expiration** (si abonnement)  
- **Technicien + groupe responsable**  
- **Lieu + inventaire**  
- Option **Autoriser le dépassement**  

Une licence peut aussi être liée à :  
- Des postes (utilisation réelle)  
- Des contrats  
- Des tickets ou changements  
- Des documents/certificats (facture, clé, attestation d’achat…)

### 5️⃣ Associer la licence aux postes
👉 Permet de savoir à tout moment :  
- Sur quels **postes** le logiciel est installé  
- Combien de **licences** sont utilisées/restantes  
- Quand elles expirent  
- Qui est responsable  

---

## 🔎 Fiche d’un objet logiciel : principaux onglets
- **🧠 Analyse d’impact** → visualiser dépendances (effet domino en cas de panne).  
- **📦 Versions** → plusieurs versions possibles.  
- **📜 Licences** → gestion des droits d’usage.  
- **💻 Installations** → postes sur lesquels la version est installée.  
- **💰 Gestion** → infos financières, facturation, fournisseurs.  
- **📃 Contrats** → contrats liés au logiciel.  
- **📁 Documents** → manuels, bons de commande, PDF liés.  
- **🧠 Base de connaissances** → articles/procédures associés.  
- **🛠️ Tickets, Problèmes, Changements** → suivi ITIL.  
- **🔗 Liens** → relier à d’autres objets GLPI.  
- **📝 Notes, Réservations, Domaines, Applicatifs, Regroupements** → usages avancés.

---

## 🎯 Exemple concret
Entreprise avec 50 postes :  
- 50 licences **Office 2021 Volume**  
- 10 licences **Adobe Acrobat Pro 2020**  
- 1 licence **LibreOffice** (logiciel libre)  

Dans GLPI :  
1. Créer 3 objets logiciels : *Office 2021*, *Acrobat Pro 2020*, *LibreOffice*.  
2. Ajouter leurs **versions**.  
3. Lier les **licences** correspondantes.  
4. Créer les **installations** en les associant aux postes.  

👉 Résultat : tu sais à tout moment :  
- ✅ Combien de licences sont utilisées  
- ✅ Où elles sont installées  
- ✅ Quand elles expirent  
- ✅ Qui est responsable
