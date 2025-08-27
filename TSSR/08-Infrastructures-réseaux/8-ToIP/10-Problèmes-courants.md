# Problèmes courants

## 📦 **Perte de paquets**

La perte de paquets est critique en ToIP. Avec UDP, aucune garantie de livraison, donc la qualité dépend :

➡️ de la ligne utilisée,

➡️ du dimensionnement du réseau,

➡️ de la mémoire tampon des switchs.

Un réseau **peut tolérer jusqu'à 20% de pertes** pour une qualité **acceptable**, mais il faut **<1% pour une bonne qualité**.

Tous les équipements intermédiaires (switchs, routeurs) sont concernés. Une surcharge ponctuelle = dégradation si aucune **priorisation** des flux n’est mise en place.

📌 En projet ou audit ToIP : désengorger interconnexions, équipements, et liens critiques.



## ⏱️ **Délai de latence**

Le **temps de transit** des paquets (latence) devient gênant à partir de quelques **dizaines de ms**.

Causes : encombrement, surcharge, traitement lent.

💡 Solutions :

➡️ augmenter les débits,

➡️ remplacer les équipements obsolètes,

➡️ segmenter + prioriser les flux via la QoS.

⚠️ Tous les équipements du chemin doivent être optimisés pour réduire la latence.



## 📈 **Gigue (Jitter)**

La gigue = **variation de délai** entre paquets d’un même flux.

C’est une **conséquence directe** des problèmes précédents.

➡️ Solution partielle : buffer en réception (augmente le délai, mais réduit la gigue).

🎯 Objectifs :

- Perte < 1%
- Latence < 100 ms
- Gigue < 20 ms  
  Un seul critère dépassé = **qualité dégradée**.



### 🔁 **Phénomènes d’écho**

Deux types d’écho en ToIP :

1.  **Écho électronique** (chambre d’écho)  
    ➕ Copies du signal avec délai (~400 ms = perceptible).  
    ➡️ Provoqué par composants électromécaniques/électroniques.

2.  **Écho acoustique  **
    ➕ Micro capte le son du haut-parleur.  
    ➡️ Contré par mécanismes **EC (Echo Cancellation)** intégrés aux passerelles VoIP.

