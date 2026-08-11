# 📐 Lab 2 — VLSM Subnetting & Static Routing

![Cisco](https://img.shields.io/badge/Cisco-CCNA1-blue?style=for-the-badge&logo=cisco&logoColor=white)
![GNS3](https://img.shields.io/badge/GNS3-2.x-orange?style=for-the-badge&logo=gns3)
![Status](https://img.shields.io/badge/Status-✅%20Completed-brightgreen?style=for-the-badge)
![Lab](https://img.shields.io/badge/GNS3%20Series-Lab%202-purple?style=for-the-badge)
![Topic](https://img.shields.io/badge/Topic-VLSM%20%2F%20Subnetting-red?style=for-the-badge)

---

## 📋 Description

Deuxième lab de la série GNS3. Découpage d'un bloc d'adresses en sous-réseaux
de tailles variables (VLSM) pour deux sites distants (Cotonou et Porto-Novo),
chacun avec 2 PC connectés via un switch, puis routage statique entre les sites.

### Objectifs
- ✅ Calculer un plan d'adressage **VLSM** à partir d'un bloc unique
- ✅ Appliquer les sous-réseaux sur 2 routeurs et leurs LAN respectifs
- ✅ Configurer le **routage statique** entre les sites
- ✅ Vérifier la connectivité de bout en bout (4 PC)

---

## 🖥️ Équipements

| Équipement | Modèle | Nom | Rôle |
|-----------|--------|-----|------|
| 🔀 Routeur | c7200/vios | R1 | Site Cotonou |
| 🔀 Routeur | c7200/vios | R2 | Site Porto-Novo |
| 🔀 Switch | IOSvL2 | Switchl2-1 | LAN Cotonou |
| 🔀 Switch | IOSvL2 | Switchl2-2 | LAN Porto-Novo |
| 💻 PC | VPCS | PC1, PC2 | Utilisateurs Cotonou |
| 💻 PC | VPCS | PC3, PC4 | Utilisateurs Porto-Novo |

---

## 🗺️ Topologie

<img width="1920" height="1080" alt="Topologie" src="https://github.com/user-attachments/assets/d946f75b-3c6d-4531-85bc-acce866054df" />



---

## 📊 Plan d'adressage VLSM

**Bloc de départ : 192.168.100.0/24**

| Réseau | Besoin | Sous-réseau | Masque | Plage utile | Passerelle |
|--------|--------|-------------|--------|-------------|------------|
| LAN Cotonou (PC1, PC2) | 50 hôtes | 192.168.100.0/26 | 255.255.255.192 | .1 → .62 | 192.168.100.1 |
| LAN Porto-Novo (PC3, PC4) | 20 hôtes | 192.168.100.64/27 | 255.255.255.224 | .65 → .94 | 192.168.100.65 |
| Liaison R1-R2 | 2 hôtes | 192.168.100.96/30 | 255.255.255.252 | .97 → .98 | — |

---

## ⚙️ Configuration complète

### 🔧 Task 1 — R1

```cisco
enable
configure terminal
hostname R1
interface gigabitEthernet1/0
ip address 192.168.100.1 255.255.255.192
no shutdown
exit
interface gigabitEthernet2/0
ip address 192.168.100.97 255.255.255.252
no shutdown
exit
ip route 192.168.100.64 255.255.255.224 192.168.100.98
end
write
```

### 🔧 Task 2 — R2

```cisco
enable
configure terminal
hostname R2
interface gigabitEthernet1/0
ip address 192.168.100.65 255.255.255.224
no shutdown
exit
interface gigabitEthernet2/0
ip address 192.168.100.98 255.255.255.252
no shutdown
exit
ip route 192.168.100.0 255.255.255.192 192.168.100.97
end
write
```

### 🔧 Task 3 — Switchl2-1

```cisco
enable
configure terminal
hostname Switchl2-1
interface range gigabitEthernet0/0 - 1
switchport mode access
no shutdown
exit
end
write
```

### 🔧 Task 4 — Switchl2-2

```cisco
enable
configure terminal
hostname Switchl2-2
interface range gigabitEthernet0/0 - 1
switchport mode access
no shutdown
exit
end
write
```

### 🔧 Task 5 — PC1, PC2, PC3, PC4 (VPCS)

! PC1
ip 192.168.100.2 255.255.255.192 192.168.100.1

! PC2
ip 192.168.100.3 255.255.255.192 192.168.100.1

! PC3
ip 192.168.100.66 255.255.255.224 192.168.100.65

! PC4
ip 192.168.100.67 255.255.255.224 192.168.100.65


---

## 🧪 Tests finaux

```cisco
R1# show ip route              ✅ route vers .64/27 visible
R2# show ip route              ✅ route vers .0/26 visible
PC1> ping 192.168.100.1        ✅ passerelle Cotonou
PC1> ping 192.168.100.3        ✅ PC1 → PC2 (même LAN)
PC1> ping 192.168.100.66       ✅ PC1 → PC3 (inter-sites)
PC3> ping 192.168.100.2        ✅ PC3 → PC1 (retour)
```

---

## 💡 Points clés

| 🔑 Concept | 📖 Explication |
|------------|-----------------|
| `/26` = 62 hôtes utiles | Suffisant pour LAN Cotonou (2 PC + marge croissance) |
| `/27` = 30 hôtes utiles | Suffisant pour LAN Porto-Novo |
| `/30` = 2 hôtes utiles | Standard pour liaisons point-à-point |
| `interface range gi0/0 - 1` | Configure plusieurs ports en une seule commande |

---

## 👨‍💻 Auteur

**Urbain Sedami Landjidé**
🎓 Étudiant en 2ème année — Licence Professionnelle
📡 Réseaux Informatique Mobilité Sécurité (RMS)
🏫 Cisco Networking Academy
📍 Cotonou, Bénin 🇧🇯

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connecter-blue?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/urbain-sedami-landjide-9b49043a8/)

---

## 📄 Licence

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
