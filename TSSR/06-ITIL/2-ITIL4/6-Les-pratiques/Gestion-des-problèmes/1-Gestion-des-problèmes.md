# Gestion des problèmes**🎯 Objectif principal >** Réduire la **probabilité** ou l’**impact** des incidents 👉 En **trouvant la cause réelle** et en la traitant à la racine.



**🔍 Définitions essentielles**

**🔥 Problème**

Une **cause inconnue** d’un ou plusieurs incidents.

- Un problème peut **déjà avoir causé des incidents** ou être **susceptible d’en causer**.
- Il **ne se gère pas en live**, mais **en analyse approfondie** → **back-office**.
- **Exemple** : Tous les matins à 9h, le serveur rame → incidents multiples → **ouverture d’un problème** pour chercher *pourquoi*.



**⚠️ Erreur connue**

Problème **identifié mais pas encore résolu**

- On connaît **le "pourquoi"**, mais on n’a **pas encore la solution définitive**.
- Elle est **documentée dans la KEDB** (Knowledge Error Database).
- Sert à **aider les techniciens niveau 1** à réagir rapidement.



**🛠️ Solution de contournement**

Une **astuce** pour **limiter l’impact**, sans résoudre le fond du problème.

- Elle **évite une panne** ou en **réduit la gravité**.
- Peut être **temporaire**… mais parfois elle reste en place longtemps 😅

**Exemple** : Le logiciel plante quand on importe un CSV trop gros → Solution de contournement = scinder le fichier en deux.



**🏗️ Quand ouvrir un problème ?**

- 📌 Incidents **récurrents**
- 🔥 **Incident majeur**
- 🧠 **Comportement anormal** sans explication

Tous les incidents **ne justifient pas** un problème. Mais **chaque problème vise à éviter des incidents futurs**. C’est une **démarche proactive**.



**🔁 Liens avec les autres pratiques**

| **Pratique** | **Rôle** |
|----|----|
| **Gestion des incidents** | Traite **l’effet**, dans l’urgence (temps réel) |
| **Gestion des problèmes** | Cherche **la cause**, dans la durée (analyse) |
| **Gestion des changements** | Sert à **appliquer la solution** trouvée |
| **KEDB** | Regroupe les **erreurs connues** et les **solutions temporaires** |
