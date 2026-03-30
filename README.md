# -Scan-de-vuln-rabilit-s-avec-Kali-Linux
# RAPPORT DE TEST D’INTRUSION

## 1. Résumé exécutif

Ce rapport présente les résultats d’un test d’intrusion réalisé sur la machine cible 192.168.85.128. L’objectif était d’identifier les vulnérabilités exploitables, d’évaluer les risques associés et de proposer des recommandations de remédiation.

L’analyse a révélé plusieurs services exposés, notamment un serveur web Apache, un service SSH sur un port non standard et une API sécurisée associée à une plateforme de supervision (Wazuh). Certaines configurations laissent entrevoir des surfaces d’attaque potentielles.

---

## 2. Périmètre du test

* Cible : 192.168.85.128
* Type de test : Boîte noire
* Méthodologie : Reconnaissance, énumération, analyse de vulnérabilités
* Outils utilisés : Nmap, Gobuster, Curl, Hydra

---

## 3. Méthodologie

Le test s’est déroulé en plusieurs phases :

### 3.1 Reconnaissance

Identification des services exposés via scan réseau.

### 3.2 Énumération

Analyse détaillée des services accessibles.

### 3.3 Identification des vulnérabilités

Recherche de failles exploitables.

### 3.4 Exploitation (théorique)

Validation des vecteurs d’attaque potentiels.

---

## 4. Résultats du scan

### Ports ouverts identifiés

| Port  | Service    | Version       | Description              |
| ----- | ---------- | ------------- | ------------------------ |
| 80    | HTTP       | Apache 2.4.52 | Serveur web par défaut   |
| 1514  | Inconnu    | -             | Service non identifié    |
| 1515  | tcpwrapped | -             | Service filtré           |
| 3333  | SSH        | OpenSSH 8.9p1 | Accès distant sécurisé   |
| 55000 | HTTPS      | API Wazuh     | Interface de supervision |

---

## 5. Analyse des vulnérabilités

### 5.1 Serveur Web (Port 80)

**Observation :**

* Page par défaut Apache visible

**Risques :**

* Divulgation d’informations
* Mauvaise configuration du serveur

**Recommandations :**

* Désactiver la page par défaut
* Mettre en place un serveur applicatif sécurisé
* Activer des en-têtes de sécurité

---

### 5.2 Service SSH (Port 3333)

**Observation :**

* Port non standard utilisé

**Risques :**

* Attaques par brute force
* Faible sécurité des identifiants

**Recommandations :**

* Désactiver l’authentification par mot de passe
* Utiliser des clés SSH
* Mettre en place Fail2Ban

---

### 5.3 Ports 1514 / 1515

**Observation :**

* Services non identifiés

**Risques :**

* Surface d’attaque inconnue

**Recommandations :**

* Identifier précisément les services
* Restreindre l’accès via firewall

---

### 5.4 API Wazuh (Port 55000)

**Observation :**

* API accessible mais protégée (HTTP 401)
* Certificat auto-signé

**Risques :**

* Mauvaise gestion de l’authentification
* Exposition d’API sensible

**Recommandations :**

* Restreindre l’accès à l’API
* Utiliser une authentification forte
* Vérifier les configurations de sécurité

---

## 6. Évaluation des risques

| Vulnérabilité          | Gravité | Impact              |
| ---------------------- | ------- | ------------------- |
| Page Apache par défaut | Faible  | Information leakage |
| SSH exposé             | Moyen   | Accès non autorisé  |
| Services inconnus      | Moyen   | Risque non maîtrisé |
| API Wazuh exposée      | Élevé   | Accès au SIEM       |

---

## 7. Plan de remédiation détaillé (solutions à apporter)

Afin de renforcer la sécurité globale du système, les actions suivantes doivent être mises en œuvre par ordre de priorité :

### 7.1 Sécurisation réseau (Priorité ÉLEVÉE)

* Mettre en place un pare-feu (iptables, ufw ou firewall matériel)
* Restreindre l’accès aux ports sensibles :

  * Port 3333 (SSH) → accès uniquement depuis IP de confiance
  * Port 55000 (API Wazuh) → accès interne uniquement (VPN recommandé)
* Fermer les ports inutilisés (1514, 1515 si non essentiels)
* Mettre en place une segmentation réseau (VLAN / DMZ)

---

### 7.2 Durcissement du serveur SSH (Priorité ÉLEVÉE)

* Désactiver l’authentification par mot de passe
* Activer l’authentification par clé SSH uniquement
* Modifier la configuration SSH :

  * Désactiver root login
  * Limiter les utilisateurs autorisés
* Installer un mécanisme anti-bruteforce (Fail2Ban)

---

### 7.3 Sécurisation du serveur Web Apache (Priorité MOYENNE)

* Supprimer la page par défaut Apache
* Désactiver l’indexation des répertoires
* Ajouter des en-têtes de sécurité :

  * X-Frame-Options
  * X-Content-Type-Options
  * Content-Security-Policy
* Mettre en place HTTPS (certificat valide via Let’s Encrypt)
* Maintenir Apache à jour

---

### 7.4 Sécurisation de l’API Wazuh (Priorité ÉLEVÉE)

* Restreindre l’accès à l’API (firewall + whitelist IP)
* Mettre en place une authentification forte (token sécurisé, MFA si possible)
* Remplacer le certificat auto-signé par un certificat valide
* Désactiver l’exposition publique de l’API
* Surveiller les logs d’accès API

---

### 7.5 Gestion des services inconnus (1514 / 1515) (Priorité MOYENNE)

* Identifier précisément les services (audit interne)
* Désactiver les services non nécessaires
* Documenter leur usage
* Restreindre leur accès via firewall

---

### 7.6 Supervision et détection (Priorité ÉLEVÉE)

* Activer la journalisation centralisée
* Configurer des alertes de sécurité (Wazuh déjà présent)
* Mettre en place un SOC ou une supervision continue
* Surveiller les tentatives de connexion SSH et API

---

### 7.7 Gestion des mises à jour (Priorité ÉLEVÉE)

* Mettre à jour régulièrement :

  * Système d’exploitation
  * Apache
  * OpenSSH
  * Wazuh
* Mettre en place un processus de patch management

---

### 7.8 Bonnes pratiques organisationnelles

* Mettre en place une politique de mots de passe robuste
* Sensibiliser les administrateurs à la cybersécurité
* Réaliser des audits de sécurité réguliers
* Documenter l’architecture et les accès

---

## 8. Conclusion

Le système présente une surface d’attaque modérée avec plusieurs points d’entrée potentiels. Bien que certaines protections soient en place, des améliorations sont nécessaires pour renforcer la posture de sécurité globale.

---

## 9. Annexes

### Commande Nmap utilisée

```
nmap -A -p- 192.168.85.128
```

### Résumé technique

* OS : Linux (Ubuntu probable)
* Services principaux : Apache, SSH, Wazuh

---

**Fin du rapport**

