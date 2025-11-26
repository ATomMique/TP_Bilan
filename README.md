# TP SISR – Docker : Installation de WordPress et Zabbix

##  Description
Ce TP montre la mise  en place d'une VM Debian 12 avecun script qui installe Docker et Docker Compose.  
Il déploie deux services principaux :

- **WordPress** (avec MariaDB)  
- **Zabbix** (avec PostgreSQL)

Aussi avec un script pour automatiser les tâches

Le but ? Faire un devoir bilan sur le B2 qui contient du git, du  docker et comment ces outils fonctionnent
---

##  Contenu du projet

- `install_docker.sh` : script bash pour installer Docker et Docker Compose  
- `docker-compose.yml` : stack Docker pour WordPress et Zabbix  
- `README.md` : documentation du projet  

---

##  Installation et utilisation

### 1️⃣ Installer Docker sur la VM

```bash
chmod +x install_docker.sh
./install_docker.sh
