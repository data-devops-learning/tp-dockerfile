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
Déployer WordPress en production avec deux conteneurs isolés (WordPress / MySQL), reliés via un réseau Docker, avec persistance des données

## Objectifs
- Créer un réseau dédié
- Créer des volumes persistants
- Démarrer la base de données
- Lancer l’application WordPress
- Vérifier la persistance après suppression des conteneurs
- (Bonus) Ajouter phpMyAdmin

---

## Étape 1 : Réseau
Tâche : Créer un réseau nommé (ex: wp_network) 
Indiquer la commande utilisée ici

---

## Étape 2 : Volumes
Tâche : Créer deux volumes :
- Volume pour WordPress (fichiers applicatifs / uploads)
- Volume pour MySQL (données de la base)

Lister ensuite les volumes pour vérifier leur création.

---

## Étape 3 : Conteneur MySQL
Tâches :
1. Lancer un conteneur MySQL (image 8.x)
2. Définir :
   - Mot de passe root
   - Base par défaut
   - Utilisateur et mot de passe applicatif
3. Attacher le volume persistant
4. Rattacher au réseau créé
5. Vérifier l’état via les commandes d’inspection/logs

Variables à définir (exemple) :
- MYSQL_ROOT_PASSWORD = ?
- MYSQL_DATABASE = ?
- MYSQL_USER = ?
- MYSQL_PASSWORD = ?

---

## Étape 4 : Conteneur WordPress
Tâches :
1. Lancer le conteneur WordPress
2. Publier le port HTTP (externe → interne)
3. Fournir les variables d’environnement pour la connexion MySQL
4. Attacher le volume persistant
5. Accéder à l’URL locale et compléter l’installation initiale (titre, admin, etc.)

Noter ici l’URL d’accès choisie.

---

## Étape 5 : Test de persistance
Procédure à exécuter :
1. Arrêter les deux conteneurs
2. Les supprimer sans détruire les volumes
3. Les recréer à l’identique
4. Vérifier que :
   - Le site WordPress conserve sa configuration
   - Les données MySQL sont intactes

Observation / Résultat attendu :
- Décrire ce qui confirme la persistance

---

## Étape 6 : Bonus
Choisir une ou plusieurs sous-tâches :
- Inspecter le réseau Docker et identifier les conteneurs attachés
- Ajouter un conteneur d’administration (phpMyAdmin)
- Accéder à l’interface d’administration MySQL via ce conteneur
- Vérifier la résolution DNS interne (ping / connexion)
- Lister les volumes et indiquer leur point de montage réel (facultatif)

---

## Vérifications finales (Checklist)
- [ ] Réseau créé
- [ ] Volumes créés
- [ ] Conteneur MySQL fonctionnel
- [ ] Conteneur WordPress accessible
- [ ] Installation initiale effectuée
- [ ] Persistance validée après recréation
- [ ] Bonus réalisé (au moins un)
- [ ] Plan de migration Compose rédigé

---

## Points de réflexion
- Pourquoi séparer applicatif et base ?
- Différence entre volume nommé et bind mount ?
- Risques liés aux mots de passe en clair ?
- Quelles optimisations sécurité ajouter (ex: non-root, backups) ?

---

## Prochaines étapes suggérées
- Introduire docker-compose.yml
- Ajouter un reverse proxy (HTTPS)
- Sauvegardes automatisées (dump)
- Surveillance (logs / métriques)
- Séparation fine de wp-content dans un volume dédié

Fin de l’énoncé.
