# Micro-Plateforme E-Learning

PDR 2 — Micro‑Plateforme d'E‑Learning (DotNet / ASP.NET Core 8 + PostgreSQL)

---

## Prérequis

| Outil | Version minimale |
|---|---|
| [.NET SDK](https://dotnet.microsoft.com/download) | 8.0 |
| [PostgreSQL](https://www.postgresql.org/download/) | 14+ |

---

## Configuration locale (première fois)

### 1. Créer la base de données PostgreSQL

Connectez-vous à PostgreSQL (pgAdmin ou psql) et créez la base :

```sql
CREATE DATABASE "MicroLmsDb";
```

> Le nom de la base (`MicroLmsDb`), l'hôte (`localhost`) et le port (`5432`) sont ceux par défaut.  
> Si vous utilisez des valeurs différentes, utilisez l'**Option C** ci-dessous.

---

### 2. Déclarer votre mot de passe PostgreSQL

Choisissez **une seule** des options suivantes.

#### Option A — Variable de session (recommandée, temporaire)

Valable uniquement pour la session PowerShell en cours. À répéter à chaque ouverture de terminal.

```powershell
$env:POSTGRES_PASSWORD = "votre_mot_de_passe"
```

#### Option B — Variable utilisateur permanente (Windows)

Persistante entre les sessions, sans toucher au code.

```powershell
[System.Environment]::SetEnvironmentVariable("POSTGRES_PASSWORD", "votre_mot_de_passe", "User")
```

Fermez et rouvrez votre terminal pour que la variable soit prise en compte.

#### Option C — Chaîne de connexion complète (si hôte/port/user/db différents)

Remplace entièrement la connexion par défaut.

```powershell
$env:MICROLMS_CONNECTION_STRING = "Host=localhost;Port=5432;Database=MicroLmsDb;Username=postgres;Password=votre_mot_de_passe;Search Path=public,lms;Include Error Detail=true"
```

> **Important :** Ne commitez jamais un mot de passe réel dans `appsettings.json`.  
> La valeur `CHANGE_ME` dans ce fichier est un marqueur volontaire — elle doit rester telle quelle dans le dépôt.

---

### 3. Lancer le projet

```powershell
.\start-web.ps1
```

Le script :
- lit `POSTGRES_PASSWORD` (ou `MICROLMS_CONNECTION_STRING` si défini),
- construit la chaîne de connexion,
- démarre `dotnet run` sur `http://localhost:5230`.

---

## Migrations et données initiales (automatiques)

Ces comportements sont contrôlés dans `E-learningProject.Web/appsettings.json` :

```json
"Database": {
  "ApplyMigrationsOnStartup": true,         // applique les migrations EF Core au démarrage
  "AutoSeedAcademicDataOnStartup": true,    // injecte les données académiques de base
  "ImportOpenContentOnStartup": false,      // import de contenu externe (désactivé par défaut)
  "SeedDemoDataOnStartup": false            // données de démonstration (désactivé par défaut)
}
```

Au premier démarrage, la structure complète de la base est créée et les données académiques sont injectées automatiquement — **aucune commande SQL manuelle n'est nécessaire**.

---

## Compte administrateur par défaut

Défini dans `appsettings.json` à la racine :

| Champ | Valeur |
|---|---|
| Email | `admin@elearning.local` |
| Mot de passe | `Admin123` |

---

## Récapitulatif des variables d'environnement

| Variable | Rôle | Priorité |
|---|---|---|
| `POSTGRES_PASSWORD` | Mot de passe PostgreSQL (user `postgres`, db `MicroLmsDb`) | 2 |
| `MICROLMS_CONNECTION_STRING` | Chaîne de connexion complète (surcharge tout) | 1 (prioritaire) |

Si aucune des deux n'est définie, le script utilise `postgres` comme mot de passe par défaut (uniquement en local).
