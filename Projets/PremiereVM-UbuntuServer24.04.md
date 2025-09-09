# Projet : Installation et Configuration VM Ubuntu Server

## Contexte du Projet
Dans le cadre de mon apprentissage de l'administration système, j'ai configuré une machine virtuelle Ubuntu Server 24.04 LTS sur Proxmox VE avec accès distant sécurisé.

## Objectifs Techniques
- Déployer une VM optimisée pour l'apprentissage
- Configurer un réseau statique stable
- Intégrer l'accès distant via VPN mesh
- Documenter les bonnes pratiques

## Architecture Implémentée

### Infrastructure
```
Serveur Physique: Lenovo M710q
Hyperviseur: Proxmox VE 8.0
VM: Ubuntu Server 24.04 LTS
Réseau: Subnet routing via Tailscale
```

### Spécifications VM
```
CPU: 2 vCPUs (host passthrough)
RAM: 4 GB avec memory ballooning
Stockage: 32 GB SSD avec optimisations VirtIO
Réseau: Paravirtualisé VirtIO
```

## Défis Rencontrés et Solutions

### 1. Configuration Réseau Statique
**Défi :** Netplan ignorait la configuration IP fixe et maintenait DHCP

**Diagnostic :**
- Syntaxe YAML corrompue dans le fichier de configuration
- Cache systemd-networkd persistant
- Coexistence problématique DHCP/statique

**Solution :**
- Recréation propre du fichier netplan avec syntaxe validée
- Redémarrage forcé du service systemd-networkd
- Nettoyage manuel des adresses IP conflictuelles

### 2. Intégration Hyperviseur
**Défi :** Qemu Guest Agent échouait au démarrage initial

**Cause :** Canal de communication Proxmox ↔ VM non établi lors de l'installation

**Solution :** 
- Installation de l'agent post-déploiement
- Redémarrage VM pour établir la communication
- Validation de l'intégration dans l'interface Proxmox

### 3. Accès Distant Sécurisé
**Défi :** Subnet routing Tailscale ne fonctionnait pas

**Diagnostic :**
- IP forwarding configuré après démarrage Tailscale
- Routes subnet non approuvées dans l'interface d'administration
- Ordre de configuration critique

**Solution :**
- Séquence correcte : IP forwarding → Restart Tailscale → Configuration routes
- Approbation manuelle des routes dans l'interface web
- Tests de validation de bout en bout

## Configuration Finale

### Réseau
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: no
      addresses:
        - 192.168.x.x/24
      routes:
        - to: default
          via: 192.168.x.x
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
```

### Services
- SSH activé et sécurisé
- Qemu Guest Agent intégré
- Firewall configuré au niveau hyperviseur
- Accès distant via subnet router Tailscale

## Validation et Tests

### Tests de Connectivité
- Accès local réseau (192.168.x.x/24)
- Connectivité internet et résolution DNS
- SSH local et distant
- Intégration Proxmox (snapshots, monitoring)

### Tests d'Accès Distant
- Connexion depuis différents réseaux
- Performance réseau acceptable
- Sécurité par chiffrement end-to-end

## Compétences Développées

### Techniques
- **Virtualisation :** Configuration avancée Proxmox, optimisations performance
- **Réseau :** Configuration statique, troubleshooting netplan, VPN mesh
- **Système :** Administration Ubuntu Server, services systemd, integration tools

### Méthodologiques
- **Troubleshooting :** Diagnostic systématique de problèmes complexes
- **Documentation :** Création de procédures reproductibles
- **Validation :** Tests complets de validation fonctionnelle

## Bonnes Pratiques Appliquées

### Sécurité
- Permissions restrictives sur fichiers de configuration
- Firewall activé par défaut
- Accès distant chiffré
- Utilisateur non-root pour administration

### Opérationnelles
- Backup automatique des configurations avant modification
- Validation systématique des changements
- Documentation en temps réel des procédures
- Tests de rollback

### Performance
- Optimisations VirtIO pour performance réseau/disque
- Memory ballooning pour utilisation efficace des ressources
- SSD optimizations avec support TRIM
- CPU host passthrough pour performance maximale

## Évolutions Prévues

### Court Terme
- Configuration SSH par clés
- Installation de services de base (nginx, monitoring)
- Automatisation backup VM

### Moyen Terme
- Intégration dans infrastructure de monitoring
- Scripts d'automatisation déploiement
- Templates VM standardisés

## Retour d'Expérience

Cette configuration m'a permis de comprendre les interactions complexes entre hyperviseur, système d'exploitation invité, et infrastructure réseau. Les problèmes rencontrés étaient représentatifs des défis réels en administration système, particulièrement l'importance de l'ordre des opérations et la validation systématique des configurations.

L'approche méthodique de troubleshooting s'est révélée cruciale : diagnostic avant action, validation étape par étape, et documentation des solutions pour référence future.

