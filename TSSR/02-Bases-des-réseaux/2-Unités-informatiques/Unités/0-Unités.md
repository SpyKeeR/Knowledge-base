## **🔢 Les Bits et les Octets :**

- **Bit (b)** :  
  C'est la plus petite unité de mesure en informatique. Il représente une valeur binaire, soit **0** ou **1**.

- **Octet (B)** :  
  1 octet = **8 bits**. Il est souvent utilisé pour représenter un caractère, comme une lettre dans un fichier texte.



## **🔄 Multiples de l'Octet :**

Les unités supérieures sont des multiples de l’octet et servent à mesurer de plus grandes quantités d'informations. Les multiples sont exprimés en **base 2** (système binaire) ou en **base 10** (système décimal).

### **🟢 Système Binaire (Base 2) :**

- **Kio (Kibioctet)** : 1 Kio = **2^10** octets = **1 024 octets**.
- **Mio (Mebioctet)** : 1 Mio = **2^20** octets = **1 048 576 octets**.
- **Gio (Gibioctet)** : 1 Gio = **2^30** octets ≈ **1,07 milliard d’octets**.
- **Tio (Tebioctet)** : 1 Tio = **2^40** octets ≈ **1,1 billion d’octets**.

### **🟢 Système Décimal (Base 10) :**

- **Ko (Kilooctet)** : 1 Ko = **10^3** octets = **1 000 octets**.
- **Mo (Megaoctet)** : 1 Mo = **10^6** octets = **1 000 000 octets**.
- **Go (Gigaoctet)** : 1 Go = **10^9** octets = **1 milliard d’octets**.
- **To (Téraoctet)** : 1 To = **10^12** octets = **1 trillion d’octets**.


**⚠️ Confusion entre les systèmes :**

- **Fabricants vs. Systèmes d’exploitation :  **
  Les fabricants de disques durs et autres supports de stockage utilisent souvent les unités en **base 10** (par exemple, 1 To = 1 000 000 000 000 octets).  
  Les systèmes d’exploitation, quant à eux, se réfèrent généralement aux unités en **base 2** (par exemple, 1 Tio = 1 099 511 627 776 octets). Cela peut mener à des différences dans la capacité affichée sur un disque dur ou un autre support de stockage.



## **⚡ Unités de Vitesse de Transmission :**

Dans les réseaux, on mesure souvent la vitesse de transfert des données en **bps** (bits par seconde).

- **bps (bits par seconde)** : C'est la vitesse de base pour mesurer le transfert de données.
- **Kbps (Kilobits par seconde)** : 1 Kbps = **1 000 bps**.
- **Mbps (Megabits par seconde)** : 1 Mbps = **1 000 000 bps**.
- **Gbps (Gigabits par seconde)** : 1 Gbps = **1 000 000 000 bps**.

Ces unités sont couramment utilisées pour quantifier la vitesse de connexion dans des situations comme le transfert de fichiers ou le streaming de vidéos.


## **📐 Conversion rapide : base 10 ➡ base 2**

**✅ 524288 Kilo-octets (base 10)** ➡ Cela fait : 524288 * 1000 = 524 288 000 octets

### **🧮 Pour convertir en MiB :**

1.  Divise par 1024 pour avoir les **KiB** :  
    524 288 000 / 1024 = 512 000 KiB

2.  Divise encore par 1024 pour avoir les **MiB** :  
    512 000 / 1024 = 500 MiB

### **📌 Et si tu as un nombre à virgule, genre 4.75 MiB ?**

L’idée est de **isoler la partie entière et la partie décimale**, puis reconvertir : Partie entière : 4 MiB = 4 * 1024 = 4096 KiB / Partie décimale : .75 * 1024 = 768 KiB

Donc : 4.75 MiB = 4096 + 768 = 4864 KiB Et en octets : 4864 * 1024 = 4 976 640 octets

### **🧮 Et si tu veux faire l’inverse (MiB ➡ octets en base 10) :**

Prenons **500 MiB**

→ 500 * 1024 = 512 000 KiB

→ 512 000 * 1024 = 524 288 000 octets

→ Et donc 524 288 Kilo-octets en base 10 (Cisco style)
