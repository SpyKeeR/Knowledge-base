# Types d'événements**✅ 1. Événement *normal -*** *Tout roule ! Rien à signaler.*

- 📌 Indique que le système ou le service fonctionne **comme prévu**.
- 🔄 C’est dans la **baseline** (le comportement attendu).
- 🧠 Important pour le **monitoring en continu** (on confirme que tout est OK).

**Exemple** : Un check ping répond bien, les services sont UP.



**⚠️ 2. Événement *avertissement -*** *Attention, on s’approche du danger.*

- 🔍 Un comportement **inhabituel** est détecté.
- 🔺 Un seuil **non critique** est proche : charge CPU qui monte, espace disque en baisse…
- 🎯 Permet une **intervention préventive**, avant que ça dégénère.

**Exemple** : Un serveur a **10 % d’espace disque libre**, ou un pic de charge réseau.



**❗ 3. Événement *exception -*** *Quelque chose de pas normal est arrivé, mais ça ne gêne pas encore tout le monde.*

- 🚨 L’état n’est **plus conforme** au fonctionnement attendu.
- 😐 Peut **ne pas impacter immédiatement** les utilisateurs.
- 🧨 Si ça évolue mal, ça peut devenir un **incident**.

**Exemple** : D**isque défaillant dans un RAID 5** → pas de perte, mais on est plus tolérant à la panne ou Un **nœud de cluster éteint** → pas de coupure, mais moins de redondance.



**🔥 4. Événement *alerte -*** *Là, il faut agir tout de suite.*

- 💣 Événement **critique** → demande une **action immédiate**.
- 📊 Basé sur un **seuil prédéfini** (alertes configurées à l’avance).
- 📋 Des **consignes d'intervention** doivent déjà être en place.

**Exemple** : **Espace disque < 5 % / Température serveur > 80°C / Perte réseau complète**



**🧠 À retenir :**

| **Type**         | **Gravité**  | **Action requise ?** | **Impact potentiel**    |
|------------------|--------------|----------------------|-------------------------|
| Événement normal | ✅ Faible    | Non                  | Aucun                   |
| Avertissement    | ⚠️ Moyen     | Parfois (prévention) | Faible ou nul           |
| Exception        | ❗ Important | Peut-être (suivi)    | Éventuellement élevé    |
| Alerte           | 🔥 Critique  | Oui (immédiate)      | Élevé / Incident direct |
