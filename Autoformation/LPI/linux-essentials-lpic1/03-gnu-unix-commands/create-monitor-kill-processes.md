# ⚙️ Créer, Surveiller et Tuer Processus

**Module** : GNU and Unix Commands  
**Cours** : Create, Monitor and Kill Processes  
**Objectif** : Maîtriser gestion complète processus Linux

---

## 🔍 Processus : Concepts

### Qu'est-ce qu'un Processus ?

**Processus** = Programme **en cours d'exécution** en mémoire

**Analogie** 🏭 :
- **Programme** = Plan usine (fichier statique disque)
- **Processus** = Usine fonctionnement (instance active mémoire)
- **PID** = Numéro usine (identifiant unique)

---

### Propriétés Processus

| Propriété | Description | Exemple |
|-----------|-------------|---------|
| **PID** | Process ID (unique) | 1234 |
| **PPID** | Parent Process ID | 1 (init/systemd) |
| **UID** | User ID propriétaire | 1000 (alice) |
| **Commande** | Programme exécuté | /usr/bin/nginx |
| **État** | Running/Sleeping/Zombie | S (Sleeping) |
| **Priorité** | Nice value | 0 (défaut) |
| **Ressources** | CPU, RAM, fichiers | 2.5% CPU, 128MB RAM |

---

### États Processus

| Code | État | Description |
|------|------|-------------|
| **R** | Running | **En cours** exécution CPU |
| **S** | Sleeping | **En attente** (interruptible) |
| **D** | Disk Sleep | Attente I/O (non interruptible) |
| **T** | Stopped | **Arrêté** (SIGSTOP ou Ctrl+Z) |
| **Z** | Zombie | **Terminé** mais parent pas récupéré |
| **<** | High Priority | Priorité **élevée** (nice < 0) |
| **N** | Low Priority | Priorité **basse** (nice > 0) |
| **+** | Foreground | Premier plan terminal |

---

## 📊 ps : Lister Processus

### ps : 3 Syntaxes Différentes

**IMPORTANT** : ps accepte **3 styles options** différents

#### 1. Syntaxe UNIX (avec tirets -)

```bash
ps -e        # Tous processus
ps -f        # Format complet
ps -ef       # Tous processus format complet
ps -u alice  # Processus utilisateur alice
```

**Caractéristique** : Options **avec tiret -**

---

#### 2. Syntaxe BSD (sans tirets)

```bash
ps a         # Tous processus terminal actuel
ps u         # Format user-oriented
ps x         # Tous processus (même sans terminal)
ps aux       # Combinaison classique
```

**Caractéristique** : Options **SANS tiret**

---

#### 3. Syntaxe GNU (double tiret --)

```bash
ps --user alice      # Processus utilisateur
ps --forest          # Arborescence processus
ps --sort=-pcpu      # Trier par CPU (décroissant)
```

**Caractéristique** : Options **longues avec --**

---

### ⚠️ Attention : Même Option, Résultats Différents

```bash
# Option "u" UNIX vs BSD
ps -u alice      # UNIX : Processus de l'utilisateur alice
ps u             # BSD : Format user-oriented (colonnes détaillées)

# Option "a" UNIX vs BSD
ps -a            # UNIX : Tous processus sauf session leaders
ps a             # BSD : Tous processus avec terminal
```

**Règle** : **JAMAIS mélanger** syntaxes (choisir une et rester cohérent)

---

### ps aux : Combinaison Standard (BSD)

**Plus utilisée** : `ps aux`

```bash
ps aux
```

**Options** :
- **a** : Tous processus (tous utilisateurs)
- **u** : Format user-oriented (colonnes lisibles)
- **x** : Inclut processus **sans terminal** (daemons)

**Sortie** :

```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.2  22560  2456 ?        Ss   Jan01   0:05 /sbin/init
www-data  1234  2.5  5.3 524288 53248 ?        S    10:30   0:15 /usr/sbin/apache2
alice     5678  0.1  0.8  45632  8192 pts/0    S+   11:00   0:01 vim document.txt
```

---

### Colonnes ps aux

| Colonne | Description | Exemple |
|---------|-------------|---------|
| **USER** | Propriétaire processus | root, alice |
| **PID** | Process ID | 1234 |
| **%CPU** | % utilisation CPU | 2.5 (peut dépasser 100% multi-cores) |
| **%MEM** | % utilisation RAM | 5.3 |
| **VSZ** | Mémoire virtuelle (KB) | 524288 |
| **RSS** | Mémoire résidente (KB) | 53248 |
| **TTY** | Terminal associé | pts/0 ou ? (aucun) |
| **STAT** | État processus | S, R, Z, T |
| **START** | **Date/heure démarrage** | Jan01, 10:30 |
| **TIME** | **Temps CPU cumulé** | 0:15 (15 sec) |
| **COMMAND** | Commande exécutée | /usr/sbin/apache2 |

---

### Colonne TIME : Temps CPU

**TIME** = Temps CPU **réellement utilisé** (pas temps écoulé)

```bash
# Processus démarré il y a 2h
# TIME = 0:05  → 5 secondes CPU utilisées sur 2h

# Interprétation :
# - Processus dormait (I/O wait) majeure partie temps
# - Seulement 5 sec de calcul CPU actif
```

**1 seconde TIME** = 1 seconde à **100% d'un core**

**Exemple multi-cores** :
```
TIME 0:30 sur 15 secondes écoulées
→ Processus utilisé 2 cores à 100% simultanément (30s CPU sur 15s réel)
```

---

### Colonne %CPU : Pourcentage CPU

**%CPU** = Utilisation CPU **instantanée** (peut dépasser 100%)

```bash
%CPU = 200%  →  2 cores à 100%
%CPU = 50%   →  1 core à 50% (ou 2 cores à 25%)
%CPU = 400%  →  4 cores à 100%
```

**Calcul** : `%CPU = (temps_cpu / temps_écoulé) × 100`

---

### ps avec Filtres

#### Filtrer par PID

```bash
# Processus spécifique
ps 1234
#   PID TTY      STAT   TIME COMMAND
#  1234 pts/0    S+     0:01 vim

# Plusieurs PID
ps 1234 5678 9012
```

---

#### Filtrer par Utilisateur

```bash
# UNIX : -u user
ps -u alice
ps -u www-data

# UNIX : -U user (par UID réel)
ps -U 1000

# BSD : u user
ps u alice       # ❌ ERREUR (u = format, pas filtre)
ps aux | grep alice  # Alternative BSD
```

---

#### Filtrer par Commande

```bash
# Processus contenant "apache"
ps aux | grep apache

# OU : Utiliser pgrep (voir section suivante)
pgrep apache
```

---

### ps -ef : Format UNIX Complet

```bash
ps -ef

# Sortie :
# UID        PID  PPID  C STIME TTY          TIME CMD
# root         1     0  0 Jan01 ?        00:00:05 /sbin/init
# www-data  1234     1  0 10:30 ?        00:00:15 /usr/sbin/apache2
```

**Colonnes** :
- **PPID** : Parent Process ID
- **C** : CPU utilization (obsolète, ignore)
- **STIME** : Start time
- **CMD** : Commande complète

---

### ps lax : Vue BSD avec Priorités

**Option l** (BSD) : Vue **long format** avec priorités

```bash
ps lax

# Sortie :
# F   UID   PID  PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
# 4     0     1     0  20   0  22560  2456 -      Ss   ?          0:05 /sbin/init
# 0  1000  1234     1  20   0 524288 53248 -      S    ?          0:15 apache2
# 0  1000  5678     1  10 -10  45632  8192 -      S<   pts/0      0:01 important-task
```

**Colonnes supplémentaires** :
- **PRI** : Priority (valeur interne kernel, 0-139)
- **NI** : **Nice value** (-20 à 19)
- **WCHAN** : Waiting channel (fonction kernel attendue)

---

### ps fax : Vue Arborescence (Forest)

**Option f** (BSD) : Affiche **arborescence** processus parents/enfants

```bash
ps fax

# Sortie :
#   PID TTY      STAT   TIME COMMAND
#     1 ?        Ss     0:05 /sbin/init
#   432 ?        Ss     0:00  \_ /usr/sbin/sshd -D
#  1234 ?        Ss     0:00      \_ sshd: alice [priv]
#  1235 ?        S      0:00          \_ sshd: alice@pts/0
#  1236 pts/0    Ss     0:00              \_ -bash
#  5678 pts/0    S+     0:01                  \_ vim document.txt
```

**Lecture** :
- **Indentation** montre hiérarchie
- **\\_** représente relation parent→enfant
- **init/systemd** (PID 1) = Ancêtre tous processus

---

### ps --forest : Vue GNU Arborescence

```bash
# Équivalent GNU
ps -ef --forest

# Ou combiné
ps aux --forest
```

---

## 🔎 pgrep : Recherche Processus par Nom

### pgrep : Syntaxe

**Rôle** : Trouver **PID** processus par **nom programme**

```bash
# Syntaxe
pgrep [options] pattern

# Exemples
pgrep apache
# 1234
# 1235
# 1236

pgrep ssh
# 432
# 1234
```

---

### pgrep -a : Affichage Détaillé

**Option -a** : Affiche **PID + commande complète**

```bash
# Sans -a : Seulement PID
pgrep nginx
# 987
# 988

# Avec -a : PID + commande
pgrep -a nginx
# 987 nginx: master process /usr/sbin/nginx
# 988 nginx: worker process
```

---

### pgrep Options Utiles

```bash
# Par utilisateur
pgrep -u alice firefox

# Processus exacts (nom complet)
pgrep -x sshd

# Compter processus
pgrep -c apache
# 5

# Processus les plus récents
pgrep -n chrome
# 9876  ← PID processus chrome le plus récent

# Processus les plus anciens
pgrep -o apache
# 1234  ← PID processus apache le plus vieux
```

---

## ⚖️ Priorités : nice et renice

### Nice : Concept

**Nice** = "Gentillesse" processus envers les autres

**Échelle** : `-20` (prioritaire) à `+19` (courtois)

**Analogie** 🚗 :
- **Nice -20** = Ambulance (priorité absolue)
- **Nice 0** = Voiture normale (défaut)
- **Nice +19** = Tracteur (laisse passer tout le monde)

---

### Échelle Nice

| Nice | Priorité | Usage | Permission |
|------|----------|-------|------------|
| **-20** | **Maximale** | Processus critiques | **root seulement** |
| **-10** | Haute | Tâches importantes | **root seulement** |
| **0** | Normale (défaut) | Processus standards | **user** |
| **10** | Basse | Tâches arrière-plan | **user** |
| **19** | **Minimale** | Batch jobs non urgents | **user** |

**Permissions** :
- ✅ **User** peut : Nice **0 à +19** (baisser priorité seulement)
- ✅ **Root** peut : Nice **-20 à +19** (tous niveaux)
- ❌ **User** ne peut **PAS** : Nice négatif (augmenter priorité)

---

### nice : Lancer Processus avec Priorité

**Syntaxe** :
```bash
nice -n VALEUR commande
```

**Exemples** :

```bash
# Nice +10 (basse priorité)
nice -n 10 ffmpeg -i video.mp4 output.avi

# Nice +19 (priorité minimale)
nice -n 19 tar -czf backup.tar.gz /data

# Nice -10 (haute priorité - ROOT)
sudo nice -n -10 backup-critical.sh

# Nice 0 (défaut, inutile de préciser)
nice -n 0 program  # Équivalent : program
```

---

### renice : Modifier Priorité Processus Existant

**Syntaxe** :
```bash
renice NICE_VALEUR PID
```

**Exemples** :

```bash
# Baisser priorité processus 1234
renice 10 1234
# 1234 (process ID) old priority 0, new priority 10

# Priorité minimale processus 5678
renice 19 5678

# Haute priorité (ROOT)
sudo renice -10 9876

# Plusieurs processus
renice 15 1234 5678 9012
```

---

### renice par Utilisateur/Groupe

```bash
# Tous processus utilisateur alice
sudo renice 10 -u alice

# Tous processus groupe developers
sudo renice 15 -g developers

# Combinaison
sudo renice 5 -u alice -g www-data
```

---

### Vérifier Nice : ps lax

```bash
# Colonne NI = Nice value
ps lax

# Filtrer processus spécifique
ps lax | grep apache
# ... PRI  NI ...
# ...  20   0 ...  apache2 (nice 0)

# Après renice 10
ps lax | grep apache
# ... PRI  NI ...
# ...  30  10 ...  apache2 (nice 10)
```

**PRI vs NI** :
- **NI** (Nice) : -20 à +19 (défini par user/root)
- **PRI** (Priority) : 0 à 139 (valeur interne kernel)
- **Formule** : `PRI = 20 + NI` (approximativement)

---

## ⏸️ top : Surveillance Temps Réel

### top : Interface Interactive

**Rôle** : Surveiller processus en **temps réel** (mise à jour continue)

```bash
top
```

**Sortie** :

```
top - 12:34:56 up 5 days,  3:21,  2 users,  load average: 1.25, 1.10, 0.95
Tasks: 287 total,   2 running, 285 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.2 us,  2.1 sy,  0.0 ni, 92.5 id,  0.0 wa,  0.2 hi,  0.0 si,  0.0 st
MiB Mem :   7850.2 total,    345.6 free,   4234.8 used,   3269.8 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   3104.2 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 1234 www-data  20   0  524288  53248  12345 S   2.5   5.3   0:15.23 apache2
 5678 alice     20   0   45632   8192   4096 S   0.1   0.8   0:01.45 vim
```

---

### top : Header Expliqué

**Ligne 1** : Uptime et Load Average
```
12:34:56              → Heure actuelle
up 5 days, 3:21       → Uptime système
2 users               → Utilisateurs connectés
load average: 1.25... → Charge CPU (1min, 5min, 15min)
```

**Load Average** :
- `1.00` = 1 core à 100% en moyenne
- `4.00` sur 4 cores = 100% utilisation
- `2.00` sur 4 cores = 50% utilisation

---

**Ligne 2** : États Processus
```
Tasks: 287 total    → Total processus
2 running           → En cours exécution
285 sleeping        → En attente
0 stopped           → Arrêtés (SIGSTOP)
0 zombie            → Zombies (terminés non récupérés)
```

---

**Ligne 3** : Utilisation CPU
```
%Cpu(s):
5.2 us   → User space (programmes utilisateur)
2.1 sy   → System (kernel)
0.0 ni   → Nice (processus nice > 0)
92.5 id  → Idle (inactif) ← IMPORTANT
0.0 wa   → Wait I/O (attente disque/réseau)
0.2 hi   → Hardware interrupts
0.0 si   → Software interrupts
0.0 st   → Steal (VM, temps volé par hypervisor)
```

**92.5% idle** = CPU tranquille (seulement 7.5% utilisé)

---

**Ligne 4-5** : Mémoire RAM et Swap
```
MiB Mem:
7850.2 total      → RAM totale
345.6 free        → RAM libre
4234.8 used       → RAM utilisée
3269.8 buff/cache → Cache disque (libérable si besoin)

avail Mem: 3104.2 → RAM disponible RÉELLEMENT (free + cache libérable)
```

---

### top : Commandes Interactives

| Touche | Action |
|--------|--------|
| **h** | Help (aide) |
| **q** | Quitter |
| **k** | Kill processus (demande PID) |
| **r** | Renice (modifier priorité) |
| **M** | Trier par **mémoire** |
| **P** | Trier par **CPU** (défaut) |
| **u** | Filtrer **utilisateur** |
| **1** | Afficher **tous CPU** séparément |
| **c** | Afficher **commande complète** |
| **Espace** | Rafraîchir **immédiatement** |

---

### top : Exemples Usage

```bash
# Top normal
top

# Top utilisateur spécifique
top -u alice

# Top trier mémoire (au lancement)
top -o %MEM

# Top batch mode (non-interactif, pour logs)
top -b -n 1 > top-snapshot.txt
# -b : Batch mode
# -n 1 : Une itération seulement

# Top intervalle 2 secondes
top -d 2
```

---

### htop : Alternative Moderne

**htop** = top amélioré (plus lisible, coloré, souris)

```bash
# Installer
sudo apt install htop

# Lancer
htop
```

**Avantages** :
- ✅ Interface **couleurs**
- ✅ Navigation **souris**
- ✅ Arborescence processus
- ✅ Recherche **intégrée** (F3)
- ✅ Tuer processus **facilement** (F9)

---

## 🔫 Signaux : kill et pkill

### Signaux : Concept

**Signal** = Message envoyé à processus (commande, notification)

**Analogie** 📞 :
- **SIGTERM** = Appel téléphone poli ("Peux-tu t'arrêter ?")
- **SIGKILL** = Couper électricité ("ARRÊT IMMÉDIAT !")
- **SIGSTOP** = Pause vidéo
- **SIGCONT** = Play vidéo

---

### Signaux Principaux

| Signal | Numéro | Nom | Action | Ignorable ? |
|--------|--------|-----|--------|-------------|
| **SIGTERM** | 15 | Terminate | Arrêt **propre** (défaut) | ✅ Oui |
| **SIGKILL** | 9 | Kill | Arrêt **brutal** immédiat | ❌ **NON** |
| **SIGSTOP** | 19 | Stop | **Pause** processus | ❌ **NON** |
| **SIGCONT** | 18 | Continue | **Reprise** après pause | ✅ Oui |
| **SIGHUP** | 1 | Hangup | Relire config (daemon) | ✅ Oui |
| **SIGINT** | 2 | Interrupt | Ctrl+C (interruption) | ✅ Oui |
| **SIGQUIT** | 3 | Quit | Ctrl+\ (quit + core dump) | ✅ Oui |

---

### Signaux : Ignorables vs Non-Ignorables

**Ignorables** : Processus peut **intercepter** et **gérer** signal

```bash
# SIGTERM (15) : Processus peut :
# - Sauvegarder données
# - Fermer connexions proprement
# - Cleanup fichiers temporaires
# - Puis terminer
```

**Non-Ignorables** : Kernel **force** action (processus ne peut rien faire)

```bash
# SIGKILL (9) : Kernel tue IMMÉDIATEMENT
# - Pas de cleanup
# - Pas de sauvegarde
# - Données potentiellement perdues
# - Fichiers corrompus possibles
```

**Règle** : **TOUJOURS** essayer SIGTERM avant SIGKILL

---

### kill : Lister Signaux

```bash
# Liste tous signaux
kill -L

# Sortie :
#  1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL
#  5) SIGTRAP      6) SIGABRT      7) SIGBUS       8) SIGFPE
#  9) SIGKILL     10) SIGUSR1     11) SIGSEGV     12) SIGUSR2
# 13) SIGPIPE     14) SIGALRM     15) SIGTERM     16) SIGSTKFLT
# 17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
# ...
```

**Alternative** :
```bash
kill -l  # Minuscule (liste aussi)
```

---

### kill : Syntaxe

**Syntaxe** :
```bash
kill [SIGNAL] PID
```

**3 formats signal** :

```bash
# Numéro
kill -9 1234

# Nom complet
kill -SIGKILL 1234

# Nom court (sans SIG)
kill -KILL 1234
```

**Tous équivalents** (recommandation : **nom court** plus lisible)

---

### kill : Exemples

#### SIGTERM (15) : Arrêt Propre

```bash
# Signal par défaut (si pas précisé)
kill 1234

# Équivalent explicite
kill -15 1234
kill -TERM 1234
kill -SIGTERM 1234

# Usage : Première tentative arrêt processus
```

---

#### SIGKILL (9) : Arrêt Brutal

```bash
# Forcer arrêt immédiat
kill -9 1234
kill -KILL 1234

# Usage : Processus bloqué, ne répond plus à SIGTERM
```

**⚠️ DANGER** : Utilisé en **dernier recours** seulement

---

#### SIGHUP (1) : Reload Config

```bash
# Relire configuration (sans redémarrer)
kill -HUP 1234
kill -1 1234

# Exemple : Nginx reload
sudo kill -HUP $(cat /var/run/nginx.pid)

# OU (plus simple)
sudo nginx -s reload
```

**Interprétation par processus** :
- **SSH daemon** : Redémarre
- **Nginx/Apache** : Relit config
- **Terminal** : Ferme session

---

#### SIGSTOP (19) : Pause Processus

```bash
# Mettre en pause (freeze)
kill -STOP 1234

# Vérifier état
ps aux | grep 1234
# ... T ... (T = Stopped)

# Reprendre
kill -CONT 1234
```

**Usage** : Suspendre processus temporairement (économiser CPU)

---

### pkill : Kill par Nom Programme

**pkill** = **pgrep** + **kill**

**Syntaxe** :
```bash
pkill [SIGNAL] pattern
```

**Exemples** :

```bash
# Tuer tous processus apache (SIGTERM défaut)
pkill apache

# Tuer avec SIGKILL
pkill -9 firefox

# Reload tous nginx
sudo pkill -HUP nginx

# Tuer processus utilisateur
pkill -u alice chromium

# Tuer processus exacts
pkill -x sshd
```

**⚠️ Attention** : pkill peut tuer **plusieurs processus** (vérifier avec pgrep avant)

```bash
# Vérifier d'abord
pgrep -a apache
# 1234 apache2
# 1235 apache2
# 1236 apache2

# Puis tuer
pkill apache
```

---

### killall : Alternative pkill

```bash
# Similaire pkill mais nom exact complet
killall apache2

# Avec signal
killall -9 firefox

# Interactif (confirmation)
killall -i chromium
# Kill chromium(1234)? (y/N) y
```

---

## ⌨️ Contrôle Jobs : fg, bg, jobs

### Foreground vs Background

**Foreground** (Premier plan) :
- Processus **interactif** (occupe terminal)
- Reçoit input clavier
- Bloque prompt jusqu'à fin

**Background** (Arrière-plan) :
- Processus **non-interactif** (terminal libre)
- Continue exécution
- Prompt disponible immédiatement

---

### Raccourcis Clavier

| Raccourci | Signal | Action |
|-----------|--------|--------|
| **Ctrl+C** | **SIGINT** (2) | **Interrompt** processus (tue) |
| **Ctrl+Z** | **SIGTSTP** (20) | **Suspend** processus (pause) |
| **Ctrl+\\** | SIGQUIT (3) | Quit + core dump |
| **Ctrl+D** | (EOF) | Fin input (pas signal) |

---

### Ctrl+Z : Suspendre Processus

```bash
# Lancer commande longue
find / -name "*.log" 2>/dev/null

# Pendant exécution : Ctrl+Z
^Z
[1]+  Stopped                 find / -name "*.log" 2>/dev/null

# Prompt disponible
# Processus suspendu (État T)
```

---

### jobs : Lister Jobs Terminal

```bash
# Lister jobs session actuelle
jobs

# Sortie :
# [1]+  Stopped                 vim document.txt
# [2]   Running                 sleep 3600 &
# [3]-  Stopped                 find / -name "*.log"
```

**Colonnes** :
- **[N]** : Numéro job
- **+** : Job courant (dernier suspendu)
- **-** : Job précédent
- **État** : Stopped, Running, Done
- **Commande**

---

### fg : Ramener Foreground

**fg** = Foreground (ramener job au premier plan)

```bash
# Ramener dernier job (+)
fg

# Ramener job numéro 1
fg %1

# Ramener job numéro 2
fg %2

# Alternative : Juste % (dernier job)
%1  # Équivalent fg %1
```

**Résultat** : Job reprend exécution, occupe terminal

---

### bg : Envoyer Background

**bg** = Background (faire tourner job en arrière-plan)

```bash
# Job suspendu (Ctrl+Z)
vim document.txt
^Z
[1]+  Stopped                 vim document.txt

# Envoyer en background
bg %1
[1]+ vim document.txt &

# OU : bg seul (dernier job)
bg
```

**⚠️ Attention** : Processus interactifs (vim, nano) ne fonctionnent PAS en background

---

### & : Lancer Directement Background

```bash
# Lancer en background dès départ
sleep 3600 &
[1] 5678
# [1] : Numéro job
# 5678 : PID

# Prompt disponible immédiatement
```

**Exemples** :

```bash
# Compression longue en background
tar -czf backup.tar.gz /data &

# Script en background
./long-script.sh &

# Rediriger stdout (sinon affiche terminal)
./script.sh > output.log 2>&1 &
```

---

### Combinaison Complète

```bash
# 1. Lancer foreground
find / -name "*.conf" > results.txt

# 2. Suspendre (Ctrl+Z)
^Z
[1]+  Stopped

# 3. Lister jobs
jobs
# [1]+  Stopped                 find / -name "*.conf"

# 4. Envoyer background
bg %1
[1]+ find / -name "*.conf" > results.txt &

# 5. Continuer travailler (terminal libre)
ls
cd /tmp

# 6. Vérifier progression
jobs
# [1]+  Running                 find / -name "*.conf" > results.txt &

# 7. Si besoin ramener foreground
fg %1

# 8. Ou attendre fin
# [1]+  Done                    find / -name "*.conf" > results.txt
```

---

## 📂 lsof : Liste Fichiers Ouverts

### lsof : Concept

**lsof** = **List Open Files**

**Rôle** : Lister **tous fichiers ouverts** par processus

**"Fichier" Linux** = Tout est fichier :
- ✅ Fichiers réguliers
- ✅ Répertoires
- ✅ Sockets réseau
- ✅ Pipes
- ✅ Devices (/dev/*)

---

### lsof : Tous Fichiers Ouverts

```bash
# Liste TOUS fichiers ouverts (très long)
sudo lsof

# Sortie :
# COMMAND    PID   USER   FD   TYPE DEVICE SIZE/OFF     NODE NAME
# systemd      1   root  cwd    DIR    8,1     4096        2 /
# systemd      1   root  rtd    DIR    8,1     4096        2 /
# systemd      1   root  txt    REG    8,1  1620224  1234567 /lib/systemd/systemd
# apache2   1234   www   mem    REG    8,1   524288  7654321 /usr/lib/apache2.so
# vim       5678  alice    4u   REG    8,1    12345  9876543 /home/alice/document.txt
```

**⚠️ Permissions** : `sudo` requis pour voir fichiers processus autres utilisateurs

---

### Colonnes lsof

| Colonne | Description |
|---------|-------------|
| **COMMAND** | Nom processus |
| **PID** | Process ID |
| **USER** | Propriétaire |
| **FD** | File Descriptor (0=stdin, 1=stdout, 2=stderr, 3+=fichiers) |
| **TYPE** | Type (REG=regular file, DIR=directory, IPv4=socket) |
| **DEVICE** | Device (major,minor) |
| **SIZE/OFF** | Taille ou offset |
| **NODE** | Inode number |
| **NAME** | Nom fichier/socket |

---

### lsof -p PID : Fichiers Processus Spécifique

```bash
# Fichiers ouverts par processus 1234
sudo lsof -p 1234

# Exemple : Apache
sudo lsof -p $(pgrep apache2 | head -1)
# COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
# apache2 1234 www-data  cwd    DIR    8,1     4096       2 /
# apache2 1234 www-data  txt    REG    8,1   524288 1234567 /usr/sbin/apache2
# apache2 1234 www-data    4u  IPv4  12345      0t0     TCP *:80 (LISTEN)
# apache2 1234 www-data    5w   REG    8,1    45678 9876543 /var/log/apache2/access.log
```

**FD Types** :
- **cwd** : Current working directory
- **txt** : Program text (binary)
- **mem** : Memory-mapped file
- **4u** : FD 4 open for read/write
- **5w** : FD 5 open for write

---

### lsof Fichier : Qui Utilise Fichier ?

```bash
# Quel processus utilise ce fichier ?
sudo lsof /var/log/syslog

# Sortie :
# COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
# rsyslogd 456 root    4w   REG    8,1   123456 9876543 /var/log/syslog

# Utile pour :
# - Savoir pourquoi fichier "busy" (impossible supprimer)
# - Identifier processus loggant dans fichier
```

---

### lsof -i : Ports et Connexions Réseau

**Option -i** : Lister sockets réseau (ports TCP/UDP)

```bash
# Tous ports ouverts
sudo lsof -i

# Sortie :
# COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
# sshd     432 root    3u  IPv4  12345      0t0  TCP *:22 (LISTEN)
# apache2 1234 www     4u  IPv4  23456      0t0  TCP *:80 (LISTEN)
# nginx   5678 www     5u  IPv4  34567      0t0  TCP *:443 (LISTEN)
# chrome  9012 alice  42u  IPv4  45678      0t0  TCP 192.168.1.100:54321->93.184.216.34:443 (ESTABLISHED)
```

---

### lsof -i : Filtres Réseau

```bash
# Port spécifique
sudo lsof -i :80
sudo lsof -i :22

# Protocole spécifique
sudo lsof -i tcp
sudo lsof -i udp

# Port + protocole
sudo lsof -i tcp:443

# IPv4 seulement
sudo lsof -i 4

# IPv6 seulement
sudo lsof -i 6

# Connexions établies
sudo lsof -i -sTCP:ESTABLISHED

# Ports écoute
sudo lsof -i -sTCP:LISTEN
```

---

### lsof -u : Fichiers Utilisateur

```bash
# Fichiers ouverts par utilisateur
sudo lsof -u alice

# Fichiers ouverts par www-data
sudo lsof -u www-data

# Tous sauf utilisateur
sudo lsof -u ^root  # Tous sauf root
```

---

### lsof +D : Fichiers Répertoire

```bash
# Tous fichiers ouverts dans /var/log
sudo lsof +D /var/log

# Utile pour :
# - Savoir si partition peut être démontée
# - Identifier processus utilisant répertoire
```

---

### lsof Exemples Pratiques

#### Trouver processus utilisant port

```bash
# Qui écoute sur port 3306 (MySQL) ?
sudo lsof -i :3306
# COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
# mysqld  1234 mysql   10u  IPv4  12345      0t0  TCP *:3306 (LISTEN)

# Tuer processus
sudo kill 1234
```

---

#### Partition busy (impossible umount)

```bash
# Démonter partition
sudo umount /mnt/data
# umount: /mnt/data: target is busy

# Trouver processus utilisant partition
sudo lsof +D /mnt/data
# COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
# bash    5678 alice  cwd   DIR    8,2     4096      2 /mnt/data/project

# Fermer processus ou changer directory
cd /
sudo umount /mnt/data  # Fonctionne
```

---

#### Fichier supprimé mais espace pas libéré

```bash
# Supprimer gros fichier
rm /var/log/huge.log

# Espace pas libéré (df -h identique)
# Raison : Processus tient encore fichier ouvert

# Trouver processus
sudo lsof /var/log/huge.log
# COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
# app     1234 user    5w   REG    8,1 10737418240  123456 /var/log/huge.log (deleted)

# (deleted) = Fichier supprimé mais FD ouvert

# Solution : Redémarrer processus
sudo systemctl restart app

# OU : Truncate FD
sudo truncate -s 0 /proc/1234/fd/5
```

---

## 📋 Antisèche - Gestion Processus

### ps Commandes

| Commande | Je veux... |
|----------|-----------|
| `ps aux` | **Tous** processus (standard) |
| `ps -ef` | Tous processus format UNIX |
| `ps lax` | Processus avec **priorités** (NI) |
| `ps fax` | **Arborescence** processus |
| `ps -u alice` | Processus utilisateur alice |
| `ps 1234` | Processus PID 1234 |
| `ps aux --sort=-%cpu` | Trier par CPU (desc) |
| `ps aux --sort=-%mem` | Trier par RAM (desc) |

---

### pgrep / pkill

| Commande | Je veux... |
|----------|-----------|
| `pgrep apache` | PID processus apache |
| `pgrep -a nginx` | PID + commande nginx |
| `pgrep -u alice firefox` | Firefox utilisateur alice |
| `pkill apache` | Tuer tous apache (SIGTERM) |
| `pkill -9 firefox` | Tuer tous firefox (SIGKILL) |
| `pkill -HUP nginx` | Reload nginx |

---

### Priorités

| Commande | Je veux... |
|----------|-----------|
| `nice -n 10 cmd` | Lancer priorité basse (+10) |
| `nice -n 19 cmd` | Lancer priorité minimale |
| `sudo nice -n -10 cmd` | Lancer priorité haute (-10) |
| `renice 10 1234` | Modifier nice processus 1234 |
| `sudo renice -5 -u alice` | Nice -5 tous processus alice |

---

### Signaux

| Commande | Signal | Je veux... |
|----------|--------|-----------|
| `kill 1234` | SIGTERM (15) | Arrêt **propre** |
| `kill -9 1234` | SIGKILL (9) | Arrêt **brutal** |
| `kill -HUP 1234` | SIGHUP (1) | Reload config |
| `kill -STOP 1234` | SIGSTOP (19) | **Pause** |
| `kill -CONT 1234` | SIGCONT (18) | **Reprise** |
| `kill -L` | - | Lister signaux |

---

### Foreground/Background

| Commande | Je veux... |
|----------|-----------|
| `cmd &` | Lancer background direct |
| `Ctrl+Z` | Suspendre processus actuel |
| `jobs` | Lister jobs terminal |
| `fg %1` | Ramener job 1 foreground |
| `bg %1` | Envoyer job 1 background |
| `fg` | Ramener dernier job |

---

### lsof

| Commande | Je veux... |
|----------|-----------|
| `sudo lsof` | Tous fichiers ouverts |
| `sudo lsof -p 1234` | Fichiers processus 1234 |
| `sudo lsof /var/log/syslog` | Qui utilise ce fichier ? |
| `sudo lsof -i` | Tous ports réseau |
| `sudo lsof -i :80` | Qui écoute port 80 ? |
| `sudo lsof -i tcp` | Connexions TCP |
| `sudo lsof -u alice` | Fichiers utilisateur alice |
| `sudo lsof +D /mnt/data` | Fichiers dans /mnt/data |

---

## 🎓 Points Clés pour l'Examen

✅ **ps 3 syntaxes** : UNIX (-), BSD (sans -), GNU (--)  
✅ **ps aux** : Combinaison standard (all, user-oriented, avec/sans terminal)  
✅ **Colonnes** : USER, PID, %CPU (peut >100% multi-cores), %MEM, START, TIME  
✅ **TIME** : Temps CPU cumulé (1s = 1s à 100% un core)  
✅ **ps lax** : Affiche priorités (colonne NI)  
✅ **ps fax** : Arborescence processus parents/enfants  
✅ **pgrep** : Trouver PID par nom programme (-a détails)  
✅ **nice -n N cmd** : Lancer avec priorité (-20 à +19)  
✅ **renice N PID** : Modifier priorité processus existant  
✅ **Nice** : User peut 0→+19, root peut -20→+19  
✅ **top** : Surveillance temps réel (load average, %CPU, RAM)  
✅ **Signaux** : SIGTERM (15) propre, SIGKILL (9) brutal, SIGSTOP (19) pause, SIGCONT (18) reprise  
✅ **kill** : 3 formats (kill -9, kill -KILL, kill -SIGKILL)  
✅ **pkill** : Tuer par nom programme  
✅ **Ctrl+C** : SIGINT (interrompt), **Ctrl+Z** : SIGTSTP (suspend)  
✅ **jobs** : Lister jobs terminal  
✅ **fg/bg** : Ramener foreground/envoyer background  
✅ **&** : Lancer directement background  
✅ **lsof** : Lister fichiers ouverts (-p PID, -i ports, -u user)  
✅ **lsof -i :PORT** : Qui écoute sur port ?  
✅ **sudo** : Requis pour voir processus autres users  

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Mélanger Syntaxes ps

```bash
# ❌ ERREUR : Mélanger BSD et UNIX
ps aux -u alice
# Options incompatibles

# ✅ CORRECT : Choisir une syntaxe
ps aux | grep alice     # BSD
ps -ef -u alice         # UNIX

# Règle : PAS de tiret avec aux (BSD)
ps -aux  # ❌ Interprété comme -a -u -x (UNIX, résultat différent)
ps aux   # ✅ BSD correct
```

---

### Erreur 2 : kill -9 Trop Rapide

```bash
# ❌ MAUVAISE PRATIQUE : kill -9 direct
kill -9 1234
# Processus tué brutalement
# Données potentiellement perdues

# ✅ BONNE PRATIQUE : SIGTERM puis SIGKILL
kill 1234          # SIGTERM (propre)
sleep 5            # Attendre 5 secondes
kill -9 1234       # SIGKILL si toujours vivant

# Script automatique
if ! kill 1234 2>/dev/null; then
    echo "Already dead"
elif sleep 5 && kill -0 1234 2>/dev/null; then
    echo "Still alive, forcing..."
    kill -9 1234
else
    echo "Terminated cleanly"
fi
```

---

### Erreur 3 : Nice Négatif Sans sudo

```bash
# ❌ ERREUR : User tente nice négatif
nice -n -5 important-task
# nice: cannot set niceness: Permission denied

# ✅ SOLUTION : sudo pour nice < 0
sudo nice -n -5 important-task

# User peut seulement baisser priorité
nice -n 10 task  # OK
nice -n -10 task # Permission denied
```

---

### Erreur 4 : Renice Augmenter Priorité

```bash
# Processus lancé nice 10
nice -n 10 task &
# [1] 1234

# User tente renice 5 (augmenter priorité)
renice 5 1234
# renice: failed to set priority for 1234: Permission denied

# ❌ User ne peut PAS augmenter priorité (10 → 5)
# ✅ User peut seulement baisser (10 → 15)
renice 15 1234  # OK

# ✅ Root peut tout
sudo renice 5 1234   # OK
sudo renice -10 1234 # OK
```

---

### Erreur 5 : pkill Trop Large

```bash
# ❌ DANGER : pkill trop général
pkill python
# Tue TOUS processus python (scripts système inclus)

# ✅ SÉCURITÉ : Vérifier avec pgrep d'abord
pgrep -a python
# 1234 python /usr/local/bin/system-monitor.py
# 5678 python my-script.py

# Affiner recherche
pkill -f my-script.py  # -f : Full command line

# OU : Tuer PID spécifiques
kill $(pgrep -f my-script.py)
```

---

### Erreur 6 : lsof Sans sudo

```bash
# ❌ INCOMPLET : lsof sans sudo
lsof -i :80
# (vide ou seulement processus user courant)

# ✅ CORRECT : sudo pour TOUS processus
sudo lsof -i :80
# COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
# apache2 1234 www-data    4u  IPv4  12345      0t0  TCP *:80 (LISTEN)

# Règle : Toujours sudo avec lsof (sauf chercher ses propres processus)
```

---

### Erreur 7 : fg Processus Terminé

```bash
# Lancer job background
sleep 10 &
[1] 1234

# Attendre fin
# [1]+  Done                    sleep 10

# Tenter ramener foreground
fg %1
# bash: fg: %1: no such job

# ✅ Vérifier jobs avant fg
jobs
# (vide)  ← Job terminé

# jobs montre seulement jobs ACTIFS
```

---

### Erreur 8 : bg Processus Interactif

```bash
# Lancer vim
vim document.txt

# Suspendre (Ctrl+Z)
^Z
[1]+  Stopped                 vim

# Envoyer background
bg %1
[1]+ vim document.txt &

# ❌ PROBLÈME : vim nécessite terminal (interactif)
# Processus bloqué (attend input terminal)

# ✅ SOLUTION : fg pour processus interactifs
fg %1  # Ramener foreground

# bg OK pour : Scripts, compression, downloads
tar -czf backup.tar.gz /data &  # ✅ OK
wget https://large-file.zip &    # ✅ OK
```

---

### Erreur 9 : Oublier & Redirection Background

```bash
# ❌ ENCOMBRE TERMINAL : stdout vers terminal
./long-script.sh &
# [1] 1234
# Log line 1...
# Log line 2...
# (logs continuent affichage)

# ✅ PROPRE : Rediriger stdout/stderr
./long-script.sh > output.log 2>&1 &
# [1] 1234
# (terminal propre, logs dans fichier)

# Vérifier progression
tail -f output.log
```

---

### Erreur 10 : Confondre %CPU et TIME

```bash
# Processus tournant 1 heure
ps aux | grep my-process
# USER  PID %CPU %MEM ... TIME COMMAND
# alice 1234 0.1  2.0 ... 0:03 my-process

# ❌ CONFUSION : %CPU faible = processus lent ?
# Non ! %CPU = Utilisation ACTUELLE instantanée

# TIME = 0:03 → 3 secondes CPU sur 1 heure
# Processus passe majeure partie temps en I/O wait ou sleep

# %CPU peut être :
# - 200% = 2 cores à 100% (actif)
# - 0.1% = Mostly sleeping (normal pour daemon)

# TIME cumulatif :
# - 10:00 = 10 minutes CPU utilisées depuis démarrage
# - 1:30:00 = 1h30 CPU (sur plusieurs jours possibles)
```

---

**🎯 Prochaine étape** : Modify Process Execution Priorities & Search Text Files Using Regular Expressions

*Dernière mise à jour: 2 février 2026*
