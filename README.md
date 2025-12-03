# TP B2 Bilan – Docker : Installation de WordPress et Zabbix

##  Description
Ce TP montre la mise  en place d'une VM Debian 12 avec un script qui installe Docker et Docker Compose.  
Il déploie deux services :

- **WordPress** (avec MariaDB)  
- **Zabbix** (avec PostgreSQL)

Également avec un script pour automatiser les tâches

Le but ? Faire un devoir bilan sur le B2 qui contient du git, du  docker et comment ces outils fonctionnent
---

##  Contenu du projet

- `docker_installation.sh` : script bash pour installer Docker et Docker Compose  
- `docker-compose.yml` : Docker pour WordPress et Zabbix  
- `README.md` : documentation du projet avec GIT

---

### 1️⃣ Installer Docker sur la VM

- Premièrement vous devez mettte à jour vôtre machine :

```sudo apt update && sudo apt upgrade -y```

- Puis créer un fichier docker_installation.sh:
  
  ```nano docker_installation.sh```

- Entrer le script utilisable par le super utilisateur :

 ```bash
# Script d'installation de Docker pour un système exécuté en root

echo "=== Mise à jour du système ==="
apt update && apt upgrade -y

echo "=== Installation des dépendances ==="
apt install -y ca-certificates curl gnupg lsb-release

echo "=== Ajout de la clé GPG Docker ==="
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

echo "=== Ajout du dépôt Docker ==="
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

echo "=== Installation de Docker ==="
apt update
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "=== Installation de docker-compose ==="
curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
  -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

echo "=== Docker installé avec succès ! ==="

```

- Vous devez ensuite rendre ce script éxécutable :

```bash
chmod +x docker-installation.sh
```

- Enfin éxécuter le script pour l'installation de docker
  
```bash
./docker-installation.sh
```

### 2️⃣ Installalation de WordPress et Zabbix avec docker

- Pour installer  WordPress et Zabbix grâce à docker vous devez créer un répertoire comme TP_Bilan puis rentrer dans le répertoire.

```mkdir TP_Bilan```
```cd TP_Bilan```

- Puis rentrer le script dans un fichier nommé "docker-compose.yml"

```nano docker-compose.yml```

```bash 
version: '3.9'

services:
  # -----------------------
  # WORDPRESS + MARIADB
  # -----------------------
  db_wordpress:
    image: mariadb:10.6
    container_name: db_wordpress
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_pass
    volumes:
      - wp_db:/var/lib/mysql

  wordpress:
    image: wordpress:latest
       container_name: wordpress
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db_wordpress
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_pass
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wp_data:/var/www/html
    depends_on:
      - db_wordpress

  # -----------------------
  # ZABBIX + POSTGRES
  # -----------------------
  postgres:
    image: postgres:15
    container_name: postgres_zabbix
    restart: always
    environment:
      POSTGRES_PASSWORD: zabbix
      POSTGRES_USER: zabbix
      POSTGRES_DB: zabbix
    volumes:
      - pg_data:/var/lib/postgresql/data

  zabbix-server:
    image: zabbix/zabbix-server-pgsql:latest
    container_name: zabbix_server
    restart: always
    environment:
      DB_SERVER_HOST: postgres
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbix
      POSTGRES_DB: zabbix
    ports:
      - "10051:10051"
    depends_on:
      - postgres

  zabbix-frontend:
    image: zabbix/zabbix-web-nginx-pgsql:latest
    container_name: zabbix_frontend
    restart: always
    environment:
      DB_SERVER_HOST: postgres
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbix
      POSTGRES_DB: zabbix
      ZBX_SERVER_HOST: zabbix-server
      PHP_TZ: Europe/Paris
    ports:
      - "8081:8080"
    depends_on:
    - zabbix-server

volumes:
  wp_db:
  wp_data:
  pg_data:

```

-Lancer Wordpress et Zabbix avec Docker

```docker-compose up -d```


### 3️⃣ Vérification

- Après l'installation vous pouvez accéder aux services en rentrant "http://IP_VM:8080" pour WordPress et "http://IP_VM:8081" pour Zabbix
<img width="1336" height="921" alt="image" src="https://github.com/user-attachments/assets/89561192-e0e1-434f-9994-27e3015edd00" /> <img width="1338" height="550" alt="image" src="https://github.com/user-attachments/assets/fbe90c1f-951c-4377-afe9-9a4bcfdb0dff" />

### 4️⃣ Utilisation de GIT

- Voici les commandes pour mettre le devoir sur un dépôt GIT de votre choix
  
```
- git init -b main #Créer le dépôt local et la branche principale
- git add . #Ajouter les fichiers à Git
- git commit -m "TP B2 Bilan - Docker, WordPress, Zabbix" #Faire un commit (sauvegarde locale)
- git remote add origin https://github.com/<votre_user>/<nom_repo>.git #Lier le dépôt local à GitHub
- git push -u origin main #Envoyer les fichiers sur GitHub.
```

- (Il se peut que vous ayez du mal à vous authentifier par rapport à votre mot de passe. Je conseille alors de faire un token sur GIT est de le coller dans le terminal pour vous identifiez)

<img width="523" height="203" alt="image" src="https://github.com/user-attachments/assets/fdd06057-6c88-45ff-804f-e5cd4de7be71" />

### Si vous voulez simplement copier les fichiers et dossiers vous pouvez cloner avec GIT

```git clone https://github.com/ATomMique/TP_Bilan.git```

<img width="295" height="86" alt="image" src="https://github.com/user-attachments/assets/62e00be8-1923-470d-a064-b914a73855fc" />

### Il suffit de lancer le script avec les commandes de la partie 2️⃣
