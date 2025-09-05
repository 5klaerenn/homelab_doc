# Projet : Accès à distance

## Problématique : 
Besoin d'un accès sécurisé au HomeLab lorsque je ne suis pas connectée à mon réseau local. Je cherchais une solution sans avoir besoin d'exposer des ports. 

## Solution technique : 
**Tailscale** : VPN mesh avec l'utilisation d'un subnet routing qui me permettra d'avoir accès à l'ensemble du réseau local qui y est connecté. 

## Architecture 

`Client -> Internet -> Tailscale -> Proxmox Gateway -> Réseau Local`

## Implémentation
**VPN** : Tailscale 
**Routing** : IP forwarding (Linux) + Subnet routing
**Sécurité** : Chiffrement bout-en-bout
**Plateformes** : Multi-plateformes (mon utilisation : Linux, Android, Windows)

## Compétences développées 
### Réseau 
- Configuration VPN mesh moderne
- Subnet routing et NAT transversal

### Sécurité
- Chiffrement WireGaurd

### Administration
- Documentation de procédures
- Monitoring infrastructure

## Résultats 
- Accès au HomeLab depuis l'extérieur
- Aucun port exposé 
- Performance : < 10ms de latence ajoutée
- Coût : 0$ (plan gratuit)