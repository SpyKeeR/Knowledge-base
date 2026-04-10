# Notes CCNA - Cisco Networking

Documentation technique CCNA 200-301 (Jeremy's IT Lab).

---

## Table des matières

1. [Static Routing](#static-routing)

---

## Static Routing

### Commande ip route (Cisco IOS)

**Syntaxe** :
```
ip route <DESTINATION_NETWORK> <SUBNET_MASK> <NEXT_HOP_IP | EXIT_INTERFACE>
```

**Exemple next-hop IP (recommandé)** :
```
R1(config)# ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

**Décomposition** :
- `192.168.3.0` : Réseau destination (route vers ce réseau)
- `255.255.255.0` : Masque sous-réseau (/24)
- `192.168.12.2` : Adresse IP next-hop (routeur suivant)

**Exemple exit interface** :
```
R1(config)# ip route 192.168.3.0 255.255.255.0 GigabitEthernet0/1
```

**Next-hop IP vs Exit Interface** :

| Méthode | Avantages | Inconvénients | Recommandation |
|---------|-----------|---------------|----------------|
| **Next-hop IP** | Fonctionne multi-access networks (Ethernet), explicite | Nécessite route vers next-hop | ✅ Préféré |
| **Exit Interface** | Simple point-to-point | Ambiguïté multi-access, processus ARP | ⚠️ Point-to-point uniquement |

**Use case** :
- Next-hop IP : Ethernet, réseaux multi-accès (switchés)
- Exit interface : Liaisons point-to-point (Serial, PPP)

### Configuration complète routeur

**Exemple topologie** :
```
PC1 (192.168.1.1/24) --- [R1] --- [R2] --- [R3] --- PC2 (192.168.3.1/24)
                    192.168.12.0/24  192.168.13.0/24
```

**R1 Configuration** :
```
hostname R1

interface GigabitEthernet0/0
 description Link to R2
 ip address 192.168.12.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/1
 description LAN PC1
 ip address 192.168.1.254 255.255.255.0
 no shutdown

ip route 192.168.13.0 255.255.255.0 192.168.12.2
ip route 192.168.3.0 255.255.255.0 192.168.12.2

end
copy running-config startup-config
```

**Explication routes** :
- Route vers `192.168.13.0/24` (interconnexion R2-R3) via R2 (`192.168.12.2`)
- Route vers `192.168.3.0/24` (LAN PC2) via R2 (`192.168.12.2`)

### Commandes diagnostic

**Afficher table routing** :
```
R1# show ip route
```

**Sortie exemple** :
```
Codes: C - connected, S - static, R - RIP, ...

Gateway of last resort is not set

C    192.168.1.0/24 is directly connected, GigabitEthernet0/1
C    192.168.12.0/24 is directly connected, GigabitEthernet0/0
S    192.168.13.0/24 [1/0] via 192.168.12.2
S    192.168.3.0/24 [1/0] via 192.168.12.2
```

**Légende** :
- `C` : Connected (directement connecté)
- `S` : Static (route statique configurée)
- `[1/0]` : `[Administrative Distance / Metric]`
  * AD 1 : Static route (priorité haute)
  * Metric 0 : Distance

**Vérifier interfaces** :
```
R1# show ip interface brief
```

**Sortie exemple** :
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.12.1    YES manual up                    up
GigabitEthernet0/1     192.168.1.254   YES manual up                    up
```

**Ping test** :
```
R1# ping 192.168.3.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
```

### Troubleshooting routes statiques

**Erreurs courantes** :

**1. Typo next-hop IP** :
```
# MAUVAIS
R1(config)# ip route 192.168.3.0 255.255.255.0 192.168.12.3
# .3 n'existe pas (devrait être .2)

# Diagnostic
R1# show ip route
S    192.168.3.0/24 [1/0] via 192.168.12.3
# Route présente mais next-hop injoignable

# Correction
R1(config)# no ip route 192.168.3.0 255.255.255.0 192.168.12.3
R1(config)# ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

**2. Exit interface incorrect** :
```
# MAUVAIS
R2(config)# ip route 192.168.3.0 255.255.255.0 GigabitEthernet0/0
# G0/0 pointe vers R1, devrait être next-hop vers R3

# Diagnostic
R2# show ip route
S    192.168.3.0/24 is directly connected, GigabitEthernet0/0
# "directly connected" via mauvaise interface

# Correction
R2(config)# no ip route 192.168.3.0 255.255.255.0 GigabitEthernet0/0
R2(config)# ip route 192.168.3.0 255.255.255.0 192.168.13.3
```

**3. IP interface incorrecte** :
```
# MAUVAIS
R3(config)# interface GigabitEthernet0/0
R3(config-if)# ip address 192.168.13.2 255.255.255.0
# IP dupliquée avec R2 (devrait être .3)

# Diagnostic
R3# show ip interface brief
GigabitEthernet0/0     192.168.13.2    YES manual up                    up
# Conflit IP avec R2

# Correction
R3(config)# interface GigabitEthernet0/0
R3(config-if)# ip address 192.168.13.3 255.255.255.0
```

**4. Interface shutdown** :
```
# Diagnostic
R1# show ip interface brief
GigabitEthernet0/0     192.168.12.1    YES manual administratively down down

# Correction
R1(config)# interface GigabitEthernet0/0
R1(config-if)# no shutdown
```

**5. Masque incorrect** :
```
# MAUVAIS
R1(config)# ip route 192.168.3.0 255.255.0.0 192.168.12.2
# Masque /16 au lieu de /24

# Correction
R1(config)# no ip route 192.168.3.0 255.255.0.0 192.168.12.2
R1(config)# ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

### Méthodologie troubleshooting

**Workflow systématique** :

1. **Vérifier table routing** : `show ip route`
   - Routes présentes ?
   - Next-hop correct ?
   - Type route (C/S/R) ?

2. **Vérifier interfaces** : `show ip interface brief`
   - Status up/up ?
   - IP correctes ?
   - Pas de shutdown ?

3. **Ping next-hop** : `ping 192.168.12.2`
   - Next-hop joignable ?
   - Problème Layer 2 ?

4. **Ping destination** : `ping 192.168.3.1`
   - Route fonctionnelle ?
   - Retour trafic configuré ?

5. **Traceroute** : `traceroute 192.168.3.1`
   - Chemin complet ?
   - Où s'arrête ?

### Default route (0.0.0.0/0)

**Gateway of last resort** :
```
R1(config)# ip route 0.0.0.0 0.0.0.0 192.168.12.2
```

**Explication** :
- `0.0.0.0 0.0.0.0` : Route par défaut (catch-all)
- Utilisée si aucune route spécifique match
- Typique routeur edge vers Internet

**Vérifier** :
```
R1# show ip route
Gateway of last resort is 192.168.12.2 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 192.168.12.2
```

### Administrative Distance (priorité routes)

**Ordre priorité** (plus bas = préféré) :

| Source route | AD | Usage |
|--------------|-----|-------|
| Directly Connected | 0 | Interfaces locales |
| **Static** | **1** | Routes manuelles |
| EIGRP Summary | 5 | Routes EIGRP summarized |
| External BGP | 20 | BGP externe |
| Internal EIGRP | 90 | EIGRP interne |
| OSPF | 110 | OSPF |
| RIP | 120 | RIP |

**Modifier AD route statique** :
```
R1(config)# ip route 192.168.3.0 255.255.255.0 192.168.12.2 200
# AD = 200 (backup route, utilisée si route dynamique OSPF échoue)
```

### Pitfalls

⚠️ **Oublier no shutdown** : Interface admin down → route inutilisable  
⚠️ **Next-hop unreachable** : Route présente mais next-hop injoignable  
⚠️ **Masque incorrect** : Route trop large/étroite  
⚠️ **Route retour manquante** : Ping aller OK, retour échoue (configuration asymétrique)  
⚠️ **Exit interface multi-access** : Ambiguïté ARP → préférer next-hop IP  
⚠️ **Conflit IP** : Duplicate IP addresses → routing erratique

---

## VLANs (Virtual Local Area Networks)

### Concepts fondamentaux

**VLAN** = Segmentation logique d'un switch Layer 2 en domaines de broadcast séparés
- Isolation trafic (sécurité + performance)
- Réduction domaines collision/broadcast
- Flexibilité organisation (utilisateurs dispersés géographiquement, même VLAN)

**Range VLANs Cisco** :
- **1** : Default VLAN (tous ports par défaut) - Ne pas utiliser en production
- **2-1001** : Normal range VLANs (stockés dans `vlan.dat`)
- **1002-1005** : Réservés (Token Ring, FDDI) - Créés automatiquement, non supprimables
- **1006-4094** : Extended range (requiert VTP transparent mode)

### Configuration basique VLANs

**Création VLAN** :
```cisco
SW1(config)# vlan 10
SW1(config-vlan)# name Engineering
SW1(config-vlan)# vlan 20
SW1(config-vlan)# name HR
SW1(config-vlan)# exit
```

**Mode access (port end-device)** :
```cisco
SW1(config)# interface FastEthernet0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
```

**Assignation multiple ports** :
```cisco
SW1(config)# interface range Fa0/1-5
SW1(config-if-range)# switchport mode access
SW1(config-if-range)# switchport access vlan 10
```

**Vérification** :
```cisco
SW1# show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/10, Fa0/11
10   Engineering                      active    Fa0/1, Fa0/2, Fa0/3
20   HR                               active    Fa0/4, Fa0/5
```

### Trunk (transport plusieurs VLANs)

**Configuration trunk** :
```cisco
SW1(config)# interface GigabitEthernet0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30
SW1(config-if)# switchport trunk native vlan 99
```

**Explication** :
- `mode trunk` : Active mode trunk (tag 802.1Q)
- `allowed vlan` : Liste VLANs autorisés (sécurité, éviter broadcast inutile)
- `native vlan` : Trafic non-tagué (défaut VLAN 1, **changer pour VLAN inutilisé**)

**Vérification** :
```cisco
SW1# show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Gig0/1      on           802.1q         trunking      99

Port        Vlans allowed on trunk
Gig0/1      10,20,30

Port        Vlans allowed and active in management domain
Gig0/1      10,20,30
```

### 802.1Q Frame Tagging

**Structure trame taguée** :
```
[Dest MAC][Source MAC][802.1Q Tag (4 octets)][EtherType][Payload][FCS]
```

**802.1Q Tag (4 octets)** :
- **TPID** (Tag Protocol ID) : 2 octets = `0x8100` (identifie 802.1Q)
- **TCI** (Tag Control Information) : 2 octets
  * **PCP** (Priority Code Point) : 3 bits (QoS - 8 niveaux priorité)
  * **DEI** (Drop Eligible Indicator) : 1 bit (éligibilité drop en congestion)
  * **VID** (VLAN Identifier) : 12 bits (0-4095, donc 4096 VLANs max)

**Processus tagging** :
1. Frame entre port access → Switch ajoute tag VLAN
2. Frame transite trunk → Tag préservé
3. Frame sort port access destination → Switch retire tag

**Native VLAN** :
- Trafic **non-tagué** sur trunk
- Si frame non-taguée reçue sur trunk → Associée native VLAN
- **Mismatch native VLAN** entre switches = problème connectivité majeur

### Inter-VLAN Routing

**Problème** : VLANs = domaines broadcast isolés → Communication inter-VLAN requiert routage Layer 3

**Solution 1 : Router externe multi-interface (Legacy)** :
```
[SW1]---VLAN10---[R1 G0/0]
     ---VLAN20---[R1 G0/1]
     ---VLAN30---[R1 G0/2]
```

**Limite** : 1 câble + 1 interface par VLAN (scalabilité)

**Solution 2 : ROAS (Router on a Stick)** :
```cisco
# Configuration routeur
R1(config)# interface GigabitEthernet0/0
R1(config-if)# no shutdown

R1(config)# interface GigabitEthernet0/0.10
R1(config-subif)# encapsulation dot1q 10
R1(config-subif)# ip address 192.168.10.254 255.255.255.0

R1(config)# interface GigabitEthernet0/0.20
R1(config-subif)# encapsulation dot1q 20
R1(config-subif)# ip address 192.168.20.254 255.255.255.0
```

**Explication** :
- **Sous-interfaces** : Interfaces logiques (G0/0.10, G0/0.20)
- `encapsulation dot1q X` : Tag 802.1Q avec VLAN ID
- Une seule interface physique (`G0/0`) pour tous VLANs
- `no shut` sur interface physique active toutes sous-interfaces

**Configuration switch (trunk vers routeur)** :
```cisco
SW1(config)# interface GigabitEthernet0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30
```

**Limite ROAS** : Trafic inter-VLAN fait aller-retour switch → routeur → switch (latence)

**Solution 3 : Switch Layer 3 + SVIs (Modern)** :
```cisco
# Activation routing
SW1(config)# ip routing

# Création SVIs (Switch Virtual Interfaces)
SW1(config)# interface vlan 10
SW1(config-if)# ip address 192.168.10.254 255.255.255.0
SW1(config-if)# no shutdown

SW1(config)# interface vlan 20
SW1(config-if)# ip address 192.168.20.254 255.255.255.0
SW1(config-if)# no shutdown
```

**Explication SVIs** :
- **Interface virtuelle** représentant VLAN en Layer 3
- Switch route trafic **en interne** (pas besoin routeur externe)
- SVI = Gateway par défaut pour hosts du VLAN
- **Condition SVI UP** : VLAN existe + au moins 1 port actif dans VLAN

**Routed port (Layer 3 sur switch)** :
```cisco
SW1(config)# interface GigabitEthernet0/1
SW1(config-if)# no switchport
SW1(config-if)# ip address 10.0.0.1 255.255.255.252
```

**Explication** :
- `no switchport` : Transforme port L2 (switchport) en port L3 (routed)
- Permet assigner IP directement (comme interface routeur)
- Utilisé connexions point-to-point switch-routeur

### Comparaison architectures Inter-VLAN

| Architecture | Câbles | Latence | Scalabilité | Complexité | Use case |
|--------------|--------|---------|-------------|------------|----------|
| **Multi-interface** | N (N=VLANs) | Basse | Très faible | Faible | Lab/Petit réseau |
| **ROAS** | 1 trunk | Moyenne | Moyenne | Moyenne | Réseau moyen |
| **Switch L3 + SVI** | 1 L3 link | Très basse | Haute | Haute | Entreprise/Campus |

### Bonnes pratiques VLANs

✅ **Ne jamais utiliser VLAN 1** : VLAN par défaut, cible attaques  
✅ **Native VLAN inutilisé** : VLAN 99, 999 sans port assigné  
✅ **Limiter VLANs autorisés trunk** : Éviter `all`, lister uniquement nécessaires  
✅ **Nommer VLANs explicitement** : `name Engineering` vs VLAN10  
✅ **Voice VLAN séparé** : Téléphonie IP isolée (QoS, sécurité)  
✅ **Management VLAN dédié** : SSH/Telnet/SNMP VLAN séparé  
✅ **DTP désactivé** : `switchport nonegotiate` (éviter auto-negotiation trunk)  

### Commandes diagnostic VLANs

**Vérifier VLANs** :
```cisco
SW1# show vlan brief
SW1# show vlan id 10
```

**Vérifier trunk** :
```cisco
SW1# show interfaces trunk
SW1# show interfaces GigabitEthernet0/1 switchport
```

**Vérifier SVIs** :
```cisco
SW1# show ip interface brief
SW1# show interfaces vlan 10
```

**Vérifier routing (switch L3)** :
```cisco
SW1# show ip route
SW1# show run | include ip routing
```

**Debug VLAN assignment** :
```cisco
SW1# show mac address-table
SW1# show interfaces Fa0/1 switchport
```

### Troubleshooting VLANs

**Problème** : PC ne communique pas inter-VLAN

**Checklist** :
1. **VLAN existe ?** : `show vlan brief`
2. **Port dans bon VLAN ?** : `show vlan id 10`
3. **Trunk configured ?** : `show interfaces trunk`
4. **VLANs allowed trunk ?** : `show interfaces trunk` (allowed list)
5. **Native VLAN match ?** : Vérifier sur les deux switches trunk
6. **Gateway configurée PC ?** : IP passerelle = SVI ou sub-interface routeur
7. **SVI UP ?** : `show ip interface brief` (Status/Protocol up/up)
8. **ip routing activé ?** (Switch L3) : `show run | include ip routing`

**Problème** : Trafic VLAN X passe sur VLAN Y

**Cause probable** : Native VLAN mismatch
```cisco
# Switch A
SW-A(config-if)# switchport trunk native vlan 99

# Switch B (ERREUR : native vlan 1)
SW-B(config-if)# switchport trunk native vlan 99  # CORRIGER
```

### VTP (VLAN Trunking Protocol)

**Concept** : Protocole Cisco propagation automatique configuration VLANs

**Modes** :
- **Server** : Créer/modifier/supprimer VLANs, propage aux clients
- **Client** : Reçoit config VLANs, ne peut modifier
- **Transparent** : Ne participe pas VTP, forward annonces (config locale uniquement)

**⚠️ Danger VTP** :
- Configuration erronée peut **effacer tous VLANs** réseau
- Switch revision number supérieure écrase config tous switches
- **Recommandation production** : VTP transparent ou désactivé

**Configuration VTP transparent (recommandé)** :
```cisco
SW1(config)# vtp mode transparent
```

---

*Notes évolutives - Compléter au fur et à mesure (Jeremy IT Days)*
