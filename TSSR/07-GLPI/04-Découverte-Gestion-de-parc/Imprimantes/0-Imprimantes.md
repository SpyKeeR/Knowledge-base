# Item Imprimantes

**🧩 Imprimante = item de configuration**

Dans GLPI, une **imprimante est un item matériel**, **au même titre qu’un ordinateur ou un switch**. On la retrouve dans le **parc matériel**.

### **🧠 Deux types de gestion :**

#### **🔄 Gestion globale (recommandée pour imprimantes réseau) :**

- L’imprimante est **créée une seule fois**.
- Elle est **liée à plusieurs ordinateurs ou équipements**.
- ✅ **Plus simple** : les modifications (ex. : changement de cartouche) sont automatiquement visibles sur toutes les machines liées.

#### **➕ Gestion unitaire :**

- Une **copie de l’imprimante** est créée pour chaque poste lié.
- ❌ Alourdit l’inventaire → à éviter sauf cas très spécifique (ex. : gestion très fine des bacs ou configurations uniques par poste).



### **🧰 Utilisation des gabarits :**

- Un **gabarit**, c’est un **modèle pré-rempli** (template) pour créer plusieurs imprimantes similaires rapidement.
- ✅ Pratique pour déployer en masse le même modèle d’imprimante dans plusieurs lieux/réseaux.



##### **🔧 Différence entre gabarit et modèle d’imprimante :**

| 🧪 **Gabarit** | Sert à créer plusieurs imprimantes avec des infos communes |
|----|----|
| 🏷️ **Modèle d’imprimante** | Sert à référencer l’imprimante (ex. : HP LaserJet M404dn) |

Le **modèle** correspond à ce qu’on retrouve sur l’étiquette du fabricant. Il permet de **lier des cartouches compatibles** au bon type d’imprimante.



### **🛢️ Gestion des cartouches :**

- Les **cartouches sont aussi des items** dans GLPI.
- On peut :
  - 🎯 **Définir des modèles de cartouches**.
  - 🔗 **Associer chaque modèle de cartouche à un modèle d’imprimante**.
  - 📦 **Ajouter les cartouches en stock** (quantité, emplacement, état).
  - 🧩 **Installer les cartouches** (logiquement) dans les imprimantes concernées.



### **🚨 Suivi de stock et alertes :**

GLPI permet :

- De **suivre le stock de cartouches** en temps réel.
- D’**associer une cartouche à une imprimante active**.
- De **définir des seuils d’alerte** pour anticiper les ruptures (ex. : alerte si < 2 cartouches restantes).

