# Writeup — Authentification, cookie de session et recherche JSON

## Objectif

> Obtenir un cookie de session via une connexion valide, puis l'utiliser avec `curl` pour rechercher le flag via une requête POST JSON vers `/search.php`.
> Identifiants : `admin` / `admin`

## Environnement

- OS local : Windows (cmd.exe)
- Outil : `curl.exe`

## Démarche

### 1. Connexion et sauvegarde du cookie de session

```cmd
curl.exe -c cookies.txt -X POST http://<IP>:<PORT>/index.php -d "username=admin&password=admin"
```

L'option `-c cookies.txt` sauvegarde les cookies renvoyés par le serveur (dont le cookie de session) dans un fichier local.

### 2. Vérification du cookie

```cmd
type cookies.txt
```

Résultat obtenu :

```
154.57.164.82   FALSE   /   FALSE   0   PHPSESSID   tecgpbbra8pkhl3lbtrd26dno5
```

### 3. Requête de recherche authentifiée

```cmd
curl.exe -b cookies.txt -X POST http://<IP>:<PORT>/search.php -H "Content-Type: application/json" -d "{\"search\":\"a\"}"
```

L'option `-b cookies.txt` renvoie le cookie sauvegardé pour rester authentifié. Une recherche vide (`""`) ne renvoie rien (`[]`), mais une lettre commune (`"a"`) fait remonter l'ensemble des résultats correspondant au filtre côté serveur (probablement une clause `LIKE '%a%'`).

## Résultat

```json
["flag: HTB{p0$t_r3p34t3r}", "Birmingham (UK)", "Glasgow (UK)", ...]
```

**Flag : `HTB{p0$t_r3p34t3r}`**

## Points clés retenus

- Sous Windows, utiliser `curl.exe` explicitement dans `cmd.exe` pour éviter l'alias PowerShell vers `Invoke-WebRequest`.
- Les guillemets dans un JSON doivent être échappés (`\"`) en ligne de commande Windows.
- Une recherche avec un caractère générique/commun permet souvent de faire remonter des données non filtrées côté serveur.
