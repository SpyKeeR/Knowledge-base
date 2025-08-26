# Process Disponibilité

**📌 Objectif du processus**

**Assurer que les services informatiques restent disponibles autant que nécessaire**, avec un minimum d’interruptions, en évaluant 3 indicateurs clés :

- **Fiabilité (Reliability)**
- **Maintenabilité (Maintainability)**
- **Disponibilité (Availability)**

On regroupe parfois ces indicateurs sous le nom de **FMD**.



## **🔧 Les 3 indicateurs principaux**

| **Indicateur** | **Définition simple** | **But** |
|----|----|----|
| **Fiabilité** | Capacité à fonctionner sans panne | Moins de pannes = plus fiable |
| **Maintenabilité** | Facilité à réparer ou remplacer | Moins de galères pour réparer = mieux |
| **Disponibilité** | % du temps où le service est opérationnel | Dépend des deux autres |

🧠 *+ tes composants sont fiables ET faciles à réparer, + ton service est dispo.*

**💡 Autres notions associées**

### **🔁 Résilience**

Capacité à continuer à fonctionner **même si une partie tombe en panne** (ex : alimentation redondante, RAID, failover, etc.)

### **🔧 Serviceability**

Facilité à **trouver du support ou des pièces de rechange** (liée au choix de prestataires sérieux)

- Constructeurs comme **HP, Cisco, Dell** → bonnes garanties, pièces disponibles
- Montage maison ou marques obscures → galère pour réparer = mauvaise serviceabilité

### **💾 Recoverability (récupérabilité)**

Capacité d’un système à **reprendre son état fonctionnel antérieur** après une panne (reprise après sinistre, backup/restauration, snapshot, etc.)

