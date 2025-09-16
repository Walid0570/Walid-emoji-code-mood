---
marp: true
theme: default
class: lead
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
header: 'WebSocket et Temps Réel'
footer: 'Présentation WebSocket | 2024'
---

# WebSocket et temps réel
## Quand la technologie transforme l'organisation


---

# WebSocket - Révolution du temps réel web
## De l'innovation technique à la transformation organisationnelle

**Évolution historique des communications web :**
- **1991** : HTTP/1.0 - Requête/Réponse simple (Tim Berners-Lee)
- **1997** : HTTP/1.1 - Connexions persistantes mais unidirectionnelles
- **2005** : AJAX - Simulation du temps réel par polling HTTP
- **2011** : **WebSocket RFC 6455** - Vraie communication bidirectionnelle
- **2024** : 89% des navigateurs supportent WebSocket

**Le défi organisationnel :**
Comment cette innovation technique transforme-t-elle les pratiques collaboratives ?

---

# Chiffres clés à retenir

![bg right:40% 80%](https://via.placeholder.com/400x300/4CAF50/white?text=WebSocket)

- **90%** de réduction de la bande passante vs polling HTTP
- **Sub-seconde** : latence WebSocket vs 3-30s polling  
- **1 million** de connexions simultanées possibles
- **11%** de navigateurs ne supportent pas WebSocket (IE anciennes versions)

---

# Définitions et technologies
## Glossaire français des concepts WebSocket

**WebSocket - Définition technique :**
> Protocole de communication **bidirectionnelle simultanée** sur connexion TCP unique

**Glossaire des technologies temps réel :**

| Terme anglais | Équivalent français | Définition |
|---------------|-------------------|------------|
| **Full-duplex** | Bidirectionnel simultané | Communication 2 sens (téléphone) |
| **Polling HTTP** | Interrogation répétée | "Y a-t-il du nouveau ?" répété |
| **Push serveur** | Notification proactive | Serveur envoie sans demande |

---

# Polling HTTP vs WebSocket
## Explication didactique

```
Polling HTTP (technique obsolète 2005-2011) :
Client: "Du nouveau ?" → Serveur: "Non" [toutes les 5 secondes]
Client: "Du nouveau ?" → Serveur: "Non" 
Client: "Du nouveau ?" → Serveur: "Oui, voici les données"

❌ Problèmes: Latence élevée, surcharge réseau
```

```
WebSocket (connexion permanente depuis 2011):
Client ↔ [CONNEXION ÉTABLIE UNE FOIS] ↔ Serveur
Serveur: "Nouvelles données !" → Client (instantané)
Client: "Action utilisateur" → Serveur (instantané)

✅ Avantages: Temps réel, économie bande passante
```

---

# Leslie Lamport - Père des systèmes distribués
## Théorie appliquée aux WebSocket

![bg right:30% 80%](https://via.placeholder.com/300x300/2196F3/white?text=Lamport)

**Leslie Lamport (Prix Turing 2013)**
Informaticien théoricien, inventeur de Paxos

**3 concepts clés appliqués aux WebSocket :**

🕒 **Horloges logiques** = **Ordre sans horloge**
→ Messages WebSocket arrivent dans le bon ordre

🤝 **Consensus Paxos** = **Accord malgré les pannes**
→ Serveurs WebSocket se synchronisent  

🔄 **Cohérence éventuelle** = **Synchronisation garantie**
→ Tous les clients convergent vers les mêmes données

---

# Citation de Lamport

> *"Un système distribué est celui où la panne d'un ordinateur que vous ne connaissez pas peut planter le vôtre."*

**Pourquoi c'est important pour WebSocket ?**

❌ **Sans ces théories** → Messages désordonnés, conflits, incohérences

✅ **Avec ces théories** → Collaboration fluide et fiable

---

# Applications WebSocket dans l'entreprise
## Cas d'usage transformants et adoption massive

## 🤝 Collaboration temps réel
- **Google Docs** (2010) : 2 milliards de documents collaboratifs
- **Figma** (2016) : 4 millions de designers connectés
- **Slack** (2013) : 500 millions de messages/jour via WebSocket

## 📊 Monitoring et dashboards  
- **IoT** : 50 milliards d'objets connectés en 2024
- **Analytics** : Netflix suit 1000+ métriques live
- **Trading** : 100 000 transactions/seconde (<1ms latence)

---

# Chiffres d'adoption WebSocket

![bg right:40% 80%](https://via.placeholder.com/400x300/FF9800/white?text=85%25)

**Chiffres d'adoption à retenir :**

- **85%** des nouvelles applications web intègrent WebSocket
- **3x plus** d'engagement utilisateur avec interfaces temps réel  
- **60%** de réduction des coûts d'infrastructure vs polling
- **80%** de réduction du MTTR avec alertes temps réel

---

# Théorème CAP et architectures WebSocket
## Les 3 stratégies techniques

**Théorème CAP (Eric Brewer, 2000)**
Dans un système distribué, **impossible** de garantir simultanément :

| Propriété | Français | Signification |
|-----------|----------|---------------|
| **C**onsistency | Cohérence | Mêmes données partout |
| **A**vailability | Disponibilité | Système toujours accessible |  
| **P**artition tolerance | Tolérance coupures | Résiste aux pannes réseau |

---

# STRATÉGIE CP - "Mode Bancaire"
## Cohérence + Tolérance aux Partitions

![bg right:40% 80%](https://via.placeholder.com/400x300/F44336/white?text=BANK)

**Applications types :**
- Trading haute fréquence, banque, e-commerce critique
- Systèmes où incohérence = perte financière

**Chiffres clés CP :**
- **Latence** : 50-200ms (consensus requis)
- **Disponibilité** : 99.9% (arrêts si coupure réseau)
- **Coût** : 3x plus cher (redondance forte)
- **Use case** : 1 transaction = 0 erreur tolérée

---

# STRATÉGIE AP - "Mode Social"  
## Disponibilité + Tolérance aux Partitions

![bg right:40% 80%](https://via.placeholder.com/400x300/4CAF50/white?text=SOCIAL)

**Applications types :**
- Réseaux sociaux, collaboration créative, gaming
- Disponibilité > cohérence parfaite

**Chiffres clés AP :**
- **Latence** : 10-50ms (pas de consensus global)
- **Disponibilité** : 99.99% (tolérance maximale)
- **Coût** : 1x (économique)
- **Use case** : 1000 messages > quelques doublons OK

---

# Architecture CP vs AP
## Comparaison technique

```
ARCHITECTURE CP (Mode Bancaire):
Client ←→ Répartiteur ←→ Serveur Principal ←→ Base Maître
                           ↓
                    Réplication Synchrone
                           ↓  
                    Serveurs Esclaves
```

```
ARCHITECTURE AP (Mode Social):
Client ←→ CDN Edge ←→ Nœud Régional ←→ Stockage Global
     ↕         ↕              ↕
Multi-Maître Résolution   Cohérence
Réplication  Conflits     Éventuelle  
```

---

# Chiffres business de l'arbitrage

![bg right:40% 80%](https://via.placeholder.com/400x300/9C27B0/white?text=$$$)

**Impact financier de la latence :**

- **Amazon** : +1% latence = -1 milliard $ de CA
- **Google** : +500ms latence = -20% de trafic  
- **Facebook** : Choix AP = +40% engagement vs CP

**STRATÉGIE HYBRIDE :**
- **E-commerce** → CP pour commandes + AP pour commentaires
- **Optimisation** coût/performance selon criticité

---

# Synthèse - Les 3 transformations WebSocket
## Messages clés pour les managers SI

## 🔧 1. TRANSFORMATION TECHNIQUE
- **Avant** : HTTP polling = 500ms + surcharge réseau
- **Après** : WebSocket = 50ms + 90% économie bande passante

## 👥 2. TRANSFORMATION ORGANISATIONNELLE  
- **Avant** : Collaboration séquentielle + conflits versions
- **Après** : Co-création simultanée + sync automatique
- **Résultat** : 40% gain productivité (Figma, Google Docs)

## 📈 3. TRANSFORMATION MANAGÉRIALE
- **Avant** : Planification rigide + validation centralisée
- **Après** : Adaptation temps réel + intelligence distribuée

---

# Chiffres à retenir absolument

![bg right:40% 80%](https://via.placeholder.com/400x300/607D8B/white?text=MEMO)

## 🎯 **Métriques clés WebSocket**

- **50ms** : Latence WebSocket standard
- **90%** : Réduction bande passante vs polling  
- **40%** : Gain productivité collaboration temps réel
- **99.99%** : Disponibilité architecture AP optimisée

**Enjeu managérial :** Équilibrer réactivité et gouvernance (théorème CAP)

---

# Évolutions et enjeux futurs
## WebRTC, Edge Computing et 5G

![bg right:40% 80%](https://via.placeholder.com/400x300/3F51B5/white?text=5G)

**Technologies convergentes :**
- **WebRTC** : Communication peer-to-peer directe  
- **Edge Computing** : Traitement au plus près des utilisateurs
- **5G** : Latence ultra-faible (<1ms)

**Impact organisationnel :**
- **Hyperconnexion** : Collaboration planétaire instantanée
- **Décentralisation** : Moins de dépendance serveurs centraux
- **Nouvelles vulnérabilités** : Sécurité distribuée complexe

---

# Question clé pour les DSI

![bg right:40% 80%](https://via.placeholder.com/400x300/E91E63/white?text=?)

## 🤔 **Défi managérial 2024**

> **Comment maintenir la gouvernance dans un monde hyperconnecté temps réel ?**

**Enjeux émergents :**
- Contrôle vs agilité temps réel
- Sécurité des flux distribués  
- Formation des équipes aux nouveaux outils
- Mesure du ROI des technologies temps réel

---

# Méthodologie appliquée
## Conformité agrégation interne option D

![bg right:40% 80%](https://via.placeholder.com/400x300/795548/white?text=✓)

**✅ Focus WebSocket et historique**
- Évolution chronologique : HTTP → AJAX → WebSocket  
- Avantages techniques chiffrés : 90% réduction, 50ms latence

**🎯 Théorème CAP approfondi**  
- 3 stratégies détaillées avec architectures
- Chiffres business : Amazon, Google, Facebook

**💡 Chiffres clés mémorisables**
- 4 métriques essentielles pour l'oral

**🇫🇷 Glossaire français complet**
- Toutes technologies traduites et expliquées

---

# Questions ?

![bg right:50% 80%](https://via.placeholder.com/500x400/00BCD4/white?text=Q&A)

## Merci pour votre attention

**WebSocket : De l'innovation technique à la transformation organisationnelle**

---

<!-- 
Pour utiliser cette présentation avec Marp :

1. Installation :
   npm install -g @marp-team/marp-cli

2. Génération PDF :
   marp presentation.md --pdf

3. Génération HTML :
   marp presentation.md --html

4. Mode présentation :
   marp --server presentation.md

5. Thèmes disponibles :
   - default (utilisé ici)
   - uncover
   - gaia
-->
