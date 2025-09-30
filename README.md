# tp-dockerfile
- Créez une image docker "factiorielle" qui permets de renvoyer le factorielle d'un nombre n en entrée en lançant le script app_1.py, vous devez :
    - Définir votre repertoire de travail "factorielle"
	- Copier votre repertoire de travail dans le repertoire défini
	- Lancer le script exo1/app_1.py au démarrage du conteneur avec "number" comme un parametre input ( python exo1/app_1.py --number 5 )
	- Lancez votre conteneur en 2 méthodes ( interactive, detachée ) 
		
- Créez une image docker "hello-flask" qui permet d'afficher la valeur de la variable d'environnement NOM en lançant le script app_2.py, vous devez:
	- Définir votre repertoire de travail "app"
	- Copier votre repertoire de travail dans le répertoire défini
	- Installer les dépendances necessaire ( installer le requirements.txt ) pour lancer le script app_2.py 
	- Déclarer votre variable d'environnement NOM qui prendra comme valeur votre prénom
	- Exposer le port 9999
	- Lancer le script exo2/app_2.py au démarrage du conteneur
		
- Embarquez le dossier exo3 dans l'image docker en 2 méthodes

# Déploiement WordPress + MySQL avec Docker

## Contexte
Vous devez déployer un site WordPress en production. Chaque composant (WordPress et MySQL) tourne dans un conteneur séparé. Vous utilisez Docker pour gérer :
- L’isolation (conteneurs)
- La persistance (volumes)
- La communication (réseau Docker)

## Prérequis
- Docker installé
- Connaissance basique des commandes : `docker run`, `docker volume`, `docker network`, `docker logs`, `docker ps`

---

## Vue d’ensemble
Composants :
- MySQL (base de données)
- WordPress (application web)
- Volumes : `db_data` (données MySQL), `wp_data` (fichiers WordPress)
- Réseau : `wp_network`
- (Bonus) phpMyAdmin pour l’administration

---

## Étape 1 : Créer un réseau Docker
Permet la résolution DNS interne entre conteneurs.

```bash
docker network create wp_network
```

Vérifier :
```bash
docker network ls
```

---

## Étape 2 : Créer les volumes de persistance

```bash
docker volume create wp_data
docker volume create db_data
```

Lister :
```bash
docker volume ls
```

---

## Étape 3 : Lancer le conteneur MySQL

```bash
docker run -d \
  --name mysql_db \
  --network wp_network \
  -e MYSQL_ROOT_PASSWORD=rootpassword \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wp_user \
  -e MYSQL_PASSWORD=wp_password \
  -v db_data:/var/lib/mysql \
  mysql:8.0
```

Vérifications :
```bash
docker ps
docker logs mysql_db
```

---

## Étape 4 : Lancer le conteneur WordPress

```bash
docker run -d \
  --name wordpress_site \
  --network wp_network \
  -p 8080:80 \
  -e WORDPRESS_DB_HOST=mysql_db:3306 \
  -e WORDPRESS_DB_USER=wp_user \
  -e WORDPRESS_DB_PASSWORD=wp_password \
  -e WORDPRESS_DB_NAME=wordpress \
  -v wp_data:/var/www/html \
  wordpress:latest
```

Accès :
```
http://localhost:8080
```

Suivre l’assistant d’installation (choisir langue, titre, utilisateur admin, etc.).

---

## Étape 5 : Vérifier la persistance

Arrêter / supprimer les conteneurs :
```bash
docker stop wordpress_site mysql_db
docker rm wordpress_site mysql_db
```

Relancer avec EXACTEMENT les mêmes commandes (les volumes `wp_data` et `db_data` existent toujours)
Revenir sur `http://localhost:8080` → le site et la config doivent être intactes

---

## Étape 6 : Bonus / Extensions

### Inspecter le réseau
```bash
docker network inspect wp_network
```

### Ajouter phpMyAdmin
```bash
docker run -d \
  --name phpmyadmin \
  --network wp_network \
  -p 8081:80 \
  -e PMA_HOST=mysql_db \
  phpmyadmin/phpmyadmin
```

Accès :
```
http://localhost:8081
```

## Commandes de diagnostic utiles

```bash
docker ps
docker logs -f wordpress_site
docker exec -it mysql_db mysql -u wp_user -p
docker volume inspect db_data
docker inspect wordpress_site | grep IPAddress -n
```

---

## Nettoyage

```bash
docker stop wordpress_site mysql_db phpmyadmin
docker rm wordpress_site mysql_db phpmyadmin
docker volume rm wp_data db_data
docker network rm wp_network
```

(Ne supprimez pas les volumes si vous voulez garder les données.)

---

## Résumé Rapide

| Élément        | Nom             | Rôle                          |
|----------------|-----------------|-------------------------------|
| Réseau         | wp_network      | Communication inter-conteneurs |
| Volume MySQL   | db_data         | Données persistantes DB       |
| Volume WP      | wp_data         | Fichiers (plugins, uploads)   |
| Conteneur DB   | mysql_db        | MySQL 8.0                     |
| Conteneur WP   | wordpress_site  | Application WordPress         |
| (Option) Admin | phpmyadmin      | Interface DB                  |

---

## Étapes suivantes suggérées
1. Créer un `docker-compose.yml`
2. Ajouter des backups (dump régulier MySQL)
3. Ajouter un reverse proxy (Traefik / Nginx) + HTTPS
4. Ajouter un volume séparé pour `wp-content` (optimisation)
5. Surveiller avec `docker stats` et logs centralisés
