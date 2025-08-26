# Cycle de vie du ticket**🆕 Création d’un ticket**

Lorsqu’un ticket est créé (par un technicien ou un utilisateur), on renseigne :

- 📅 **Date d’ouverture**
- 🧾 **Titre** (clair et précis)
- 📝 **Description** du problème
- 🔁 **Type** de ticket (incident, demande, etc.)
- 🚨 **Urgence** et **impact** → définissent la **priorité**
- 🧍 **Statut** initial du ticket
- 👥 **Attribution** à un groupe, un technicien, ou un fournisseur

📌 **Attribution automatique** possible via des **règles de gestion** (ex : si le ticket contient le mot "imprimante", l’assigner au groupe "Support Matériel").



**📈 Traitement du ticket**

L’onglet "Traitement" permet de suivre et gérer tout le cycle du ticket :

- 💬 **Suivi** : historique de toutes les actions
- 📅 **Tâches** : planification de rendez-vous, appels, actions techniques
- 📎 **Documents** : ajout de fichiers utiles (captures, logs, procédures…)
- 💡 **Solution** : proposition de contournement ou de résolution définitive

🧑‍💻 L’utilisateur peut **tester la solution**, et : ✅ Clore le ticket → solution validée OU ❌ Ne pas le clore → ticket rouvert



**⚖️ Validation d’un ticket (optionnelle)**

Dans certains cas, on peut **demander une validation** du traitement :

- 👨‍🏫 Un **valideur** (chef d’équipe, collègue…) vérifie le travail d’un technicien (souvent en formation ou junior)
- 📬 Le valideur reçoit une **notification**
- ☑️ Il peut **accepter ou refuser** la résolution
- 🗒️ Il laisse une **appréciation** (commentaire)

🎯 Tant que le ticket n’est pas validé, il reste **ouvert**.



**📈 SLA – Contrats de service**

- 🎯 Un **SLA (Service Level Agreement)** définit un délai de réponse/résolution.
- Il peut être **attribué automatiquement** via les règles de GLPI.
- ❗ Modifier manuellement le SLA peut entraîner un **non-respect du contrat client**, donc **à éviter**.



**🔗 Éléments liés au ticket**

Dans l’onglet **"Éléments"**, on peut **lier** : 💻 L’ordinateur concerné, 🖨️ Une imprimante associée, 🧩 Un logiciel impliqué dans le problème

📌 Cela aide à mieux **contextualiser** l’incident et accélérer sa résolution.
