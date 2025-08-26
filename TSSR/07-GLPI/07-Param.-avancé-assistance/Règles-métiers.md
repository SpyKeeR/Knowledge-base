# Règles métiers🧠 Une fois le ticket créé (après avoir cliqué sur "Envoyer"), le gabarit n'est plus utilisé. À partir de là, **le moteur de règles métiers** entre en jeu.

🔁 Il peut se déclencher : ✳️ **À la création** d’un ticket / ✳️ **À la modification** d’un ticket / ✳️ Ou **aux deux moments**



**🧩 Fonctionnement du moteur de règles**

🧱 Les règles sont lues **dans l’ordre** ➜ **pile de règles**

🔢 L’ordre d’exécution est **primordial** (les premières règles sont appliquées avant les suivantes)

🎯 **Bonnes pratiques :** En haut : règles **génériques /** En bas : règles **spécifiques**



**🔍 Structure d’une règle métier**

**🎯 Critères de sélection**

- Exemples : **type** du ticket, **catégorie**, etc.
- Possibilité d’utiliser des **opérateurs logiques** : ET, OU

**🛠️ Actions déclenchées si critères validés**

- Modifier la **priorité**
- Réaffecter un **technicien** ou un **groupe**
- Changer le **contrat de service**
- Appliquer un **SLA (Service Level Agreement)**

**⏱️ Gestion des SLA (niveaux de service)**

**📐 SLA = engagements sur les délais**

- **TTO** (Time To Own) ⏳ : Temps de **prise en charge**
- **TTR** (Time To Resolve) ⏱️ : Temps de **résolution**

**🕐 Chaque SLA est lié à :**

- Un **calendrier** (jours ouvrés, heures d'ouverture…)
- Une **durée maximale** autorisée

⚠️ Si un SLA est dépassé :

- Pas bloquant techniquement
- Peut être problématique si lié à un **contrat externe** (risques de pénalités)

**📈 Escalades SLA (niveaux d'escalade)**

🔁 Si aucune action n’est faite dans un délai donné → **action automatique**

**🔔 Exemple :**

- Si après **20 min** : envoyer une notification
- Après **40 min** : changer la **priorité**
- Après **1h** : affecter à un **niveau 2** ou à un **manager**

🎯 Objectif : éviter que le ticket « stagne » sans action.



**🧱 Ordre de mise en œuvre (bonnes pratiques)**

1.  🔖 Créer les **catégories** de tickets  
    → Précises mais pas trop nombreuses

2.  📄 Créer les **gabarits** de tickets  
    → Liés aux catégories selon le type de demande

3.  🕒 Créer les **SLA  **
    → Avec **nomenclature claire** pour les retrouver facilement  
    → Adapter les TTO/TTR aux **enjeux métiers**

4.  📈 Définir les **escalades** SLA  
    → Critères + actions automatiques (priorité, affectation…)

5.  🧠 Créer les **règles métiers** pour appliquer tout ça  
    → Enchaînement logique basé sur critères + actions
