# 📘 Documentation Wireshark – Initiation aux Protocoles Réseaux et au Modèle OSI

---

## 1. Introduction à Wireshark

**Wireshark** est un analyseur de paquets réseau libre et open source. Il permet de capturer et visualiser le trafic circulant sur une interface réseau en temps réel. Grâce à lui, on peut **analyser le contenu des paquets** (ou trames), filtrer par protocole, IP, port, etc.

Wireshark permet aussi d’examiner les différentes **couches du modèle OSI** en désencapsulant les données contenues dans chaque trame.

---

## 2. Différence entre une trame et un paquet

* **Trame (Frame)** : unité de données au **niveau de la couche liaison (couche 2 OSI)**. Elle contient les adresses MAC source/destination, et les données utiles pour les couches supérieures.
* **Paquet (Packet)** : unité de données au **niveau de la couche réseau (couche 3 OSI)**. Il transporte des données entre adresses IP.

> En résumé : une **trame encapsule un paquet**.

---

## 3. Formats PCAP/PCAPNG

* **PCAP** : format de fichier de capture standard utilisé par Wireshark.
* **PCAPNG** (PCAP Next Generation) : version améliorée, plus flexible, supportant plusieurs interfaces, des métadonnées, des commentaires, etc.

---

## 4. Capture de paquets

Une fois Wireshark lancé en root :

1. Sélectionner l’interface réseau connectée à **Alcasar**.
2. Démarrer la capture.

Les types de paquets analysés :

---

### 🔁 Paquets ARP

#### Exemple observé :

* Adresse MAC source : `VMware_c0:00:08`
* Adresse MAC destination : `Broadcast`
* Adresse IP source : `192.168.116.27`
* Adresse IP destination : `192.168.116.1`
* Protocole : `ARP`
* Info : `Who has 192.168.116.1? Tell 192.168.116.27`

📝 **Fonction ARP** : résolution d’adresses IP en adresses MAC.

---

### 📤 Paquets UDP

#### Exemple observé :

* Source : `192.168.116.1`
* Destination : `224.0.0.251` (Multicast mDNS)
* Protocole : `UDP`
* Info : `Standard query ...`

D’autres paquets UDP observés : NTP, SSDP, mDNS, DNS

📝 **Fonction UDP** : protocole de transport non fiable (pas de vérification de réception), utilisé pour les requêtes rapides comme DNS, NTP, mDNS.

---

### 📥 Paquets TCP

#### Exemple observé :

* Échange avec `192.168.116.1`, `192.168.116.2`, `192.168.116.129`
* Connexion avec SYN, ACK, FIN visibles
* Adresse MAC/IP source et destination visibles

📝 **Fonction TCP** : protocole de transport fiable avec établissement de connexion.

---

## 5. Désencapsulation et modèle OSI

En ouvrant une trame dans Wireshark, on peut voir :

* Couche 1/2 : Ethernet II – MAC source/destination
* Couche 3 : IPv4 – IP source/destination
* Couche 4 : TCP ou UDP – Ports source/destination
* Couche 7 : Application – DNS, HTTP, etc.

---

## 6. Correspondance avec les formats ARP/UDP/TCP

### 📘 ARP

Format ARP :

| Champ         | Exemple dans la capture               |
| ------------- | ------------------------------------- |
| Hardware Type | Ethernet (1)                          |
| Protocol Type | IPv4 (0x0800)                         |
| Operation     | 1 (Request)                           |
| Sender MAC/IP | `00:0c:29:...` / `192.168.116.27`     |
| Target MAC/IP | `00:00:00:00:00:00` / `192.168.116.1` |

---

### 📘 UDP

Format UDP :

| Champ       | Valeur                 |
| ----------- | ---------------------- |
| Source Port | Exemple : 5353 (mDNS)  |
| Destination | Exemple : 5353         |
| Longueur    | Variable selon données |
| Checksum    | Présente               |

---

### 📘 TCP

Format TCP (3-Way Handshake) :

| Étape      | Drapeau TCP | Description       |
| ---------- | ----------- | ----------------- |
| 1. SYN     | SYN         | Début connexion   |
| 2. SYN-ACK | SYN, ACK    | Réponse serveur   |
| 3. ACK     | ACK         | Connexion établie |

Wireshark permet d’identifier chaque étape grâce aux flags TCP affichés dans la colonne "Info".

---

### 🔁 Diagramme du 3-Way Handshake TCP

```
Client                Serveur
  | ------ SYN ------> |
  | <---- SYN-ACK ---- |
  | ------ ACK ------> |
Connexion établie
```

---

## 7. Filtres Wireshark

Pour faciliter l’analyse, on peut utiliser des filtres d’affichage :

| Filtre Wireshark           | Effet                      |
| -------------------------- | -------------------------- |
| `arp`                      | Affiche les trames ARP     |
| `udp`                      | Affiche les paquets UDP    |
| `tcp`                      | Affiche les paquets TCP    |
| `ip.addr == 192.168.116.1` | Filtre sur adresse IP      |
| `tcp.port == 80`           | Filtre sur port TCP (HTTP) |
| `eth.addr == xx:xx:xx`     | Filtre sur adresse MAC     |

✅ Utilisation des filtres : tapez-les dans la barre de filtre en haut et appuyez sur Entrée.

---

## 8. Autres protocoles identifiés

* **SSDP** : découverte de services (UPnP)
* **NTP** : synchronisation de l’heure
* **mDNS** : résolution de noms en local
* **DNS** : résolution de noms de domaine
* **Browser** : annonce NetBIOS (ex: Windows)

