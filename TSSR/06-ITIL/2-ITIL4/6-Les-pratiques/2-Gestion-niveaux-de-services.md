# Gestion - niveaux de services

## 🎯 **1. Objectif de la pratique**

L’objectif de cette pratique est **d'assurer que les services IT livrés répondent réellement aux attentes des clients**, en termes de performance, disponibilité, support, etc.

Pour ça, il faut : 
- 🔍 **Définir des objectifs mesurables** de performance
- 📝 **Documenter ces objectifs** dans des contrats clairs
- 🎛️ **Piloter la fourniture du service** en fonction de ces objectifs



## 📑 **2. Les documents clés à connaître**

### 🧾 **SLR – Service Level Requirement** 
C’est **la demande initiale** du client : il exprime ce qu’il veut (ex : "Je veux que mon site web soit dispo 24h/24").

📌 C’est une **expression de besoin**.

### 🧾 **SLA – Service Level Agreement**
C’est **le contrat officiel** signé entre le fournisseur IT et le client. Il : Définit ce qui est fourni, Précise les niveaux de service attendus, Identifie les responsabilités des deux parties

💡 Le SLA doit **correspondre à un service défini dans le catalogue de services**.

### 🧾 **OLA – Operational Level Agreement** 
C’est un **accord interne** entre différentes équipes ou services (ex : équipe réseau et équipe support). → Il sert à **garantir que les engagements pris dans le SLA peuvent être tenus**.

Pas de pénalité, mais c’est important pour la coordination en interne.

### 🧾 **UC – Underpinning Contract** 
C’est le contrat avec un **prestataire externe** (hébergeur, fournisseur cloud…). → Il soutient le SLA et a une **valeur légale**, donc **des clauses juridiques** s’y ajoutent souvent.



## 🧩 **3. Structure typique d’un SLA**

Voici ce que contient un SLA bien fait : 
- 📦 **Modalités du service** (quoi, comment, à qui)
- 🔁 **Continuité** (en cas de panne)
- ⏳ **Disponibilité** (ex : 99,9 %)
- 🔧 **Changements** (comment ils sont gérés)
- 🚀 **Performances attendues**
- 🛠️ **Mise en production**
- 📞 **Support utilisateur**
- 🔐 **Sécurité**
- 📈 **Reporting et indicateurs**
- 💸 **Modalités financières**
- 📜 **Devoirs du client**
- 📅 **Durée du contrat**
- 🕐 **Plages horaires de support**



## 🧠 **4. Contexte concret pour ancrer les concepts**

Tu bosses en tant que tech support dans une boîte qui gère l’infrastructure de plusieurs PME. Un client t’appelle : « Mon application ne fonctionne plus depuis 5 min ! »  
Tu regardes le SLA → l’appli est en **priorité 2 (P2)** : ⏱️ Le temps de **prise en charge** du ticket est de 30 minutes, et non la résolution !

➡️ Donc tu ouvres le ticket et tu réponds dans les délais = SLA respecté ✅



## 📊 **5. Les KPI (Key Performance Indicators)**

Les **KPI** sont des **indicateurs de suivi** pour voir si le SLA est respecté.

Exemples de KPI : 
- 🔴 % de tickets P1 pris en charge dans les 15 min
- 🔵 Taux de disponibilité d’un service sur 30 jours
- 🟢 Nombre de changements déployés sans incident

💬 Ces KPI permettent à l’équipe IT de **s’auto-évaluer** et au client de **mesurer la qualité du service** rendu.



📌 **6. Attention à cette distinction essentielle :**

⚠️ **Prise en charge ≠ Résolution >** Le SLA te dit souvent en combien de temps tu dois **réagir**, mais pas forcément **résoudre** le souci.

→ Les 2 peuvent être précisés, mais il faut faire la différence !

