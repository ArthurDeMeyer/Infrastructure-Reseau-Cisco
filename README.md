# 🏢 Infrastructure Réseau Cisco - Architecture Multi-Site 

> **Simulation d'une infrastructure d'entreprise centralisée avec Siège social et Agence distante via liaison WAN.**

![Status](https://img.shields.io/badge/Status-Opérationnel_🟢-success) ![Version](https://img.shields.io/badge/Version-1.0-blue) ![Tool](https://img.shields.io/badge/Cisco-Packet_Tracer-orange)

## 📖 Description du Projet

L'objectif était de passer d'un réseau local simple à une **architecture distribuée** connectant deux sites géographiques (Lille et Paris).

La particularité de cette version est la **centralisation des services** :
* Le site distant (Paris) ne possède pas de serveur.
* Il s'appuie sur le Siège (Lille) pour obtenir ses adresses IP (DHCP) et résoudre les noms de domaine (DNS).
* Cela démontre l'utilisation avancée du **DHCP Relay** et du **Routage Inter-VLAN**.

---

## 🗺️ Topologie & Architecture

### 📍 Zone SIÈGE (Lille)
C'est le cœur du réseau. Il héberge les serveurs et gère la logique.
* **Architecture :** Router-on-a-Stick (Routage Inter-VLAN).
* **VLAN 10 (Clients) :** Postes de travail des employés du siège.
* **VLAN 20 (Serveurs) :** Zone démilitarisée interne hébergeant le DNS/DHCP et le Web.

### 📍 Zone AGENCE (Paris)
Un site distant simplifié pour les commerciaux.
* **Réseau 30 :** Réseau unique pour les clients distants.
* **Spécificité :** Aucune configuration serveur locale. Tout passe par le WAN.

### 📡 Liaison WAN
Simulation d'une ligne louée (Fibre) entre les deux routeurs.
* **Réseau :** `10.0.0.0/30` (Masque strict pour liaison point-à-point).

---

## 📊 Plan d'Adressage

| Équipement | Interface | IP / Masque | Rôle / Description |
| :--- | :--- | :--- | :--- |
| **Router0 (Lille)** | `G0/0.10` | `192.168.10.254 /24` | Gateway Clients Siège (VLAN 10) |
| | `G0/0.20` | `192.168.20.254 /24` | Gateway Serveurs (VLAN 20) |
| | `G0/1` | `10.0.0.1 /30` | Sortie WAN vers Paris |
| **Server0 (Central)**| `NIC` | `192.168.20.20 /24` | **Maître DNS & DHCP** |
| **Web Server** | `NIC` | `192.168.20.10 /24` | Hébergement Site Intranet |
| **Router1 (Paris)** | `G0/0` | `192.168.30.254 /24` | Gateway Agence |
| | `G0/1` | `10.0.0.2 /30` | Sortie WAN vers Lille |

---

## ⚙️ Configuration Technique & Protocoles

### 1. Segmentation (Switching & VLANs)
Sur le site de Lille, le trafic est ségrégué pour la sécurité.
```cisco
# Création des VLANs
vlan 10
name CLIENTS
vlan 20
name SERVERS

# Assignation des ports
int f0/1
 switchport mode access
 switchport access vlan 10
int g0/1
 switchport mode trunk  <-- Liaison vers le Routeur 
