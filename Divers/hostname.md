# Liste de commandes

## Hostname

### Afficher le nom d'hôte

- `hostnamectl`

### Changer le nom d'hôte

- **Modifier le nom d'hôte**:
  `hostnamectl set-hostname new-hostname`
  `hostnamectl set-hostname "nice display for new-hostname" --pretty`
- **Ajouter l'entrée dans `/etc/hosts`**:
  `127.0.0.1 new-hostname` en utilisant par exemple `nano /etc/hosts`
- **Relancer le service `systemd-hostnamed`**
  `systemctl restart systemd-hostnamed`
