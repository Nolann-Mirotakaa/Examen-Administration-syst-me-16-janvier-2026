# `Examen Administration Système – 16 janvier 2026`

---

## Principales distributions

Famille Debian : Debian, Ubuntu

Famille Red Hat : Red Hat Enterprise Linux, CentOS, Fedora, AlmaLinux

Autres : Arch Linux, Gentoo, openSUSE


## Positionnement et outils

Debian : stabilité et support communautaire. Outils : apt, dpkg, systemctl

Red Hat : usage entreprise avec support commercial. Outils : dnf/yum, rpm, systemctl

---

## Back to basics

* **Kernel/Superviseur :** accès complet au matériel 
* **Utilisateur :** accès limité au matériel

---

## Qui est où ?
```
Répertoire         |    Contenu                            
-------------------o--------------------------------------------------o
/etc               |  Fichiers de configuration système               |
/bin, /usr/bin     |  Binaires utilisateurs et utilitaires essentiels |
/sbin, /usr/sbin   |  Binaires administration système                 |
/home              |  Répertoires utilisateurs                        |
/var               |  Données variables                               |
/var/log           |  log du système                                  |
/var/lib           |  Données persistantes des services               |
-------------------o--------------------------------------------------o
```

---

## Où est le pilote ?

* **Liste des modules chargés :** `lsmod`
* **Infos module :** `modinfo <nom_module>`
* **Charger / décharger module :** `rmmod <nom_module>`
* **Messages du module :** `dmesg | grep <nom_module>`

---

## MS Windows

#### WSL (Windows Subsystem for Linux) : permet d’exécuter Linux sur Windows sans VM complète. Permet SSH, bash, apt et autres outils.

* Permet l'utilisation de SSH, de apt, de bash sur windows

---

## On commence

* Interdire la connection root via SSH
```
sudo nano /etc/ssh/sshd_config
# Modifier : PermitRootLogin no
sudo ufw enable
```

* Configurer la connection par clé publique/privé
* Activer le pare-feu

---

## Alerte !

Si SSH vous avertit d'un problème de clé publique d'hôte, ne jamais accepter sans vérification.

Vérifier l'empreinte de la clé avec :
```
ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub
```

Cela permet d'éviter une attaque Man-in-the-Middle

---

## Oh les gourmands !

```
du -h /home/* | sort -h
```

* Permet d’identifier les utilisateurs qui consomment le plus d’espace disque
* Pour ne voir que les 5 plus gros dossiers :
```
du -h /home/* | sort -h | tail -n 5
```

---

## On va aider les devs...

* **Debian :** `apt install build-essential gcc make gdb`
* **Red Hat :** `dnf groupinstall "Development Tools"`
* Ajouter headers et libs nécessaires  (`libc6-dev`, `kernel-devel`) sur debian
```
sudo apt update
sudo apt install build-essential gcc make gdb vim git libncurses5-dev libssl-dev
```

---

## Qui fait quoi ?

* Dernières connexions : `last`
* Identifier les sessions sudo : `grep sudo /var/log/auth.log` (Debian) ou `/var/log/secure` (Red Hat) --> Ces commandes permettent de tracer l'activité des utilisateurs et l'usage des privilèges administratifs.

---

## Post mortem

```bash
journalctl
less /var/log/syslog
less /var/log/messages
```

* Identifier erreurs et les problemes de kernel qu'il y a potentielement eu 

---

## On surveille...

```bash
systemctl is-enabled bidule    # auto-démarrage
systemctl status bidule        # état actuel
systemctl stop bidule          # arrête le service
systemctl start bidule         # Démarre le service
systemctl enable bidule        # activer auto-démarrage
journalctl -u bidule           # logs
systemctl restart bidule       # redémarrer
systemctl stop bidule && systemctl status bidule  # arrête le service et demande l'etat actuel permet d'etre sur que un service est bien arrété
```

* Identifier le paquet sur Debian : `dpkg -S $(which bidule)`
---

## Zut

* Démarrage en mode recovery / single-user
* Utilisation d’un live CD / USB pour monter le disque et réinitialiser le mot de passe root

---

## C'est la faute à Rémy

* `systemctl status apache2` : état du service
* `journalctl -u apache2` : logs du service
* `netstat -tulpn` : ports ouverts
* `curl -I http://localhost` : réponse HTTP
* Vérifie l'état du service, les logs, les ports ouverts et la réponse HTTP.

---

## Au charbon !

```
On cherche sur internet le paquet et on mets le fichier sur la machine et ensuite on fait les commandes suivantes
sudo dpkg -i elasticsearch-8.0.0-amd64.deb
sudo apt install -f
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```

* Configuration principale : /etc/elasticsearch/elasticsearch.yml
* Ces commandes installent et activent ElasticSearch sur Debian

---

## Gestion d'un serveur web multi-sites

* Paquets : `apache2`, `php`, `libapache2-mod-php`, `mysql-server`
```
sudo apt install apache2 php libapache2-mod-php mysql-server
```

* Organisation : `/var/www/site1`, `/var/www/site2`
```
On créer une arborescense qui sépare les 2 sites car ça permet de simplifier la gestion donc ici on créer 2 repertoires
```

* Logs : `/var/log/apache2/site1.access.log`, `/var/log/apache2/site2.access.log`
```
On peut créer un enregistrement en séparé des différents site en créeant 2 fichiers de Logs
Permet de savoir quelles pages ont été consultées sur chaque site
```

* Analyse des visites : `awstats`
```
sudo apt install awstats
```
