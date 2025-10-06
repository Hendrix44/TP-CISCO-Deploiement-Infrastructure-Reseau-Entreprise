# TP1 CISCO – Déploiement d’une Infrastructure Réseau d’Entreprise

##  Introduction
Ce projet a pour objectif de déployer et configurer une **infrastructure réseau complète** pour une entreprise fictive répartie sur **trois sites distants** :
- **Magasin**
- **Atelier**
- **Bureaux**

L’ensemble du travail est réalisé sur **Cisco Packet Tracer**, avec des équipements Cisco 1941, 2960 et 3560, mais il peut être transposé sur du matériel réel.

---

##  Objectifs pédagogiques
- Concevoir une **architecture réseau multi-sites** cohérente  
- Configurer les **routeurs, switchs et VLANs**  
- Mettre en œuvre le **routage statique** entre les sites  
- Déployer des **services réseau essentiels** (DHCP, DNS, Web)  
- Assurer la **connectivité complète et la sécurité de base** du réseau  

---

##  Architecture générale
L’entreprise dispose de **trois sites interconnectés** via des liens fibre optique (réseaux /30).  
Chaque site possède sa propre logique réseau et ses services internes.

###  1. Site Magasin
| Équipement | Rôle | Adressage |
|-------------|------|-----------|
| Routeur Cisco 1941 | Passerelle du site | 192.168.10.254 |
| Switch 2960 | Distribution | - |
| 3 PC (2 caisses, 1 gestion) | Postes utilisateurs | 192.168.10.x |
| Imprimante | Ressource partagée | 192.168.10.x |

- **Réseau :** `192.168.10.0/24`  
- **Adressage :** Manuel  
- **Fonction :** Vente et gestion locale  

---

### 2. Site Atelier
| Équipement | Rôle | Adressage |
|-------------|------|-----------|
| Routeur 1941 | Passerelle inter-sites | 192.168.60.254 / .70.254 / .80.254 |
| Switch 3560 | Switch de couche 3 (serveur DHCP) | - |
| 3 PC | Un par VLAN | DHCP |

- **VLANs :**
  - VLAN 60 – Responsables Production  
  - VLAN 70 – Techniciens  
  - VLAN 80 – Programmeurs CNC  
- **Réseaux :** `192.168.60.0/24`, `192.168.70.0/24`, `192.168.80.0/24`  
- **Adressage :** Dynamique via le **DHCP du switch 3560**

---

### 3. Site Bureaux
| Équipement | Rôle | Adressage |
|-------------|------|-----------|
| Routeur 1941 | Router-on-a-Stick | 192.168.20.254 / .30.254 / .40.254 / .50.254 |
| 2 Switches 2960 | Interconnexion via EtherChannel | - |
| 6 PC | Employés (Direction, Ventes, Comptabilité) | DHCP |
| Serveur DNS-DHCP | 192.168.50.100 |
| Serveur Web | 192.168.50.200 |

- **VLANs :**
  - VLAN 20 – Direction  
  - VLAN 30 – Ventes  
  - VLAN 40 – Comptabilité  
  - VLAN 50 – Serveurs  
- **Réseaux :** `192.168.20.0/24` → `192.168.50.0/24`  
- **Adressage :** Dynamique via le **serveur DNS-DHCP (192.168.50.100)**  
- **Services :** DNS, DHCP, HTTP  

---

##  Interconnexion des sites
| Lien | Réseau | Type |
|------|---------|------|
| Magasin ↔ Atelier | 209.10.10.0/30 | Fibre |
| Atelier ↔ Bureaux | 209.20.20.0/30 | Fibre |

- **Routage :** Statique (chaque routeur connaît les réseaux distants via des routes statiques)  
- **Passerelles par défaut :** `192.168.xx.254` pour chaque VLAN  

---

##  Services configurés
- **DHCP :**
  - Site Atelier → DHCP intégré au switch 3560  
  - Site Bureaux → Serveur DNS-DHCP (192.168.50.100)
- **DNS :** Résolution interne du domaine `entreprise.com`  
- **Web :** Serveur HTTP sur `192.168.50.200`  
- **Routage statique :** Entre les 3 sites via les routeurs 1941  
- **Sécurité :** Mots de passe, bannières et sécurisation des interfaces  

---

##  Fonctionnement global
1. Chaque site gère son **réseau local** via un **switch et un routeur**.  
2. Les **VLANs** isolent les départements pour une meilleure segmentation.  
3. Les **serveurs DHCP** attribuent automatiquement les adresses IP.  
4. Le **serveur DNS** central résout les noms du domaine `entreprise.com`.  
5. Le **routage statique** permet la communication inter-sites.  
6. Tous les hôtes peuvent accéder aux **services internes** (Web, impression, DNS).  

---

##  Sécurité
- Mot de passe sur les accès **console et VTY**  
- **Bannière légale** (Message of the Day)  
- Désactivation des **interfaces inutilisées**  
- Option : **SSH** pour l’administration distante  

---

##  Résultats attendus
- Ping fonctionnel entre tous les hôtes des trois sites  
- VLANs isolés mais routés via le Router-on-a-Stick  
- Adressage IP automatique via DHCP  
- Résolution DNS et accès au serveur Web  
- Infrastructure **opérationnelle et documentée**

---

## Informations techniques
- **Logiciel :** Cisco Packet Tracer  
- **Équipements simulés :** Cisco 1941, 2960, 3560  
- **Type de routage :** Statique  
- **Domaine interne :** `entreprise.com`  
- **DNS primaire :** 192.168.50.100  

---

##  Auteur
Projet pédagogique réalisé dans le cadre d’un **TP de configuration Cisco (CCNA / Réseaux d’entreprise)**.  
Rôle : **Technicien Cisco – Déploiement d’infrastructure multi-sites**

---

##  Schéma logique simplifié

```text
          [Magasin]                    [Atelier]                     [Bureaux]
      ┌────────────────┐          ┌────────────────┐           ┌─────────────────────┐
      │ Routeur 1941   │◄──────► │ Routeur 1941   │◄──────►  │ Routeur 1941 (R.o.S)│
      │ 192.168.10.254 │          │ 192.168.60/70/80 │        │ 192.168.20/30/40/50 │
      └────────────────┘          └────────────────┘           └─────────────────────┘
             │                            │                             │
         [Switch 2960]               [Switch 3560]                [Switch 2960 x2]
             │                            │                             │
      [PCs / Imprimante]         [3 VLAN / 3 PCs]       [PCs / Serveurs (DNS, Web)]
