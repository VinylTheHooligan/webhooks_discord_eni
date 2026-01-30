# 🛠️ Créer votre premier workflow GitHub Actions  
Ce document vous explique, étape par étape, comment créer un workflow simple pour envoyer un message vers **le webhook Discord de la promotion**.

L’objectif est que vous puissiez fabriquer votre propre workflow, même si vous débutez, pour ensuite faire un pull request.

> **Important :** pour envoyer un message, vous devez utiliser **`secrets.WEBHOOK_URL`**.  
> C'est une variable d'environnement tenu secrète, afin d'éviter toute action malicieuse.

---

# 1. Qu’est‑ce qu’un workflow ?
Un workflow est un fichier YAML placé dans votre dépôt GitHub, dans :

`.github/workflows/mon-workflow.yml`

GitHub Actions lit ce fichier et exécute automatiquement ce que vous avez défini.

---

# 2. Le bloc `on:` — Quand le workflow démarre
Ce bloc indique **à quel moment GitHub doit lancer votre workflow**.

Voici les trois déclencheurs les plus utiles pour débuter :

### ✔️ `workflow_dispatch`  
Permet de lancer le workflow **manuellement** depuis GitHub.

```yaml
on:
    workflow_dispatch:
```

### ✔️ `push`

Le workflow se lance quand vous poussez du code.

```yaml
on:
    push:
        branches: [ main ]
```

### ✔️ `schedule`

Permet d’exécuter automatiquement le workflow à une heure précise (CRON).

```yaml
on: 
    schedule: 
        - cron: "0 9 * * *" # Tous les jours à 08h00 (UTC+1)
```

# 3. Le bloc `jobs:` — Ce que GitHub doit faire

Un workflow contient un ou plusieurs jobs. Un job = un ensemble d’actions à exécuter.

```yaml
jobs:
    send-message:
        runs-on: ubuntu-latest
        steps:
            ...
```

# 4. `runs-on:` — Le système utilisé

GitHub exécute votre job sur une machine virtuelle. Pour débuter, utilisez simplement :

```yaml
runs-on: ubuntu-latest
```

# 5. Le bloc `steps:` — Les étapes du job

Chaque step est une action que GitHub va exécuter.

```yaml
steps:
    - name: Dire bonjour
        run: echo "Hello"
```

PS : Les steps s’exécutent dans l’ordre.

# 6. `name:` — Le nom de l’étape

C’est uniquement un titre lisible dans l’interface GitHub.

```yaml
- name: Envoyer un message vers Discord
```

# 7. `env:` — Variables d’environnement

Vous pouvez définir des variables réutilisables dans vos steps.

```yaml
env:
    MESSAGE: "Bonjour depuis GitHub Actions"
```

PS : Ici, `MESSAGE` pourra être réutiliser dans le **run**.

# 8. `run:` — Les commandes exécutées

C’est ici que vous écrivez les commandes shell. Pour envoyer un message vers le webhook Discord du serveur commun :

```yaml
run: |
    curl -X POST \
        -H "Content-Type: application/json" \
        -d "{\"content\":\"$MESSAGE\"}" \
        ${{ secrets.WEBHOOK_URL }}
```

Rappel : vous devez utiliser secrets.WEBHOOK_URL.

# 9. Exemple complet

```yaml
name: Envoi Discord

on:
    workflow_dispatch:

jobs:
    send:
        runs-on: ubuntu-latest

    env:
        MESSAGE: "Bonjour, ceci est un test depuis GitHub Actions."

    steps:
        - name: Envoyer un message vers le serveur Discord
            run: |
                curl -X POST \
                    -H "Content-Type: application/json" \
                    -d "{\"content\":\"$MESSAGE\"}" \
                    ${{ secrets.WEBHOOK_URL }}
```

# 10. Utilité d’un workflow en production

Un workflow GitHub Actions n’est pas seulement un outil "technique" : en production, il sert à automatiser des tâches répétitives, réduire les erreurs humaines et garantir que votre application reste stable, testée et déployée correctement.

Voici plusieurs exemples concrets de cas d’utilisation, accessibles même pour des débutants.

---

## ✔️ 1. Envoyer un message sur le serveur Discord commun
C’est l’exemple le plus simple et le plus immédiat dans l'utilité initial du webhook discord.

Le workflow peut envoyer un message via **le webhook Discord du serveur commun** pour :

- prévenir qu’un workflow a été lancé ;
- annoncer qu’un test a réussi ou échoué ;
- signaler qu’un déploiement est terminé ;
- notifier une erreur.

> Pour envoyer un message, vous devez utiliser :  
> **`secrets.WEBHOOK_URL`**

---

## ✔️ 2. Prévenir lorsqu’un commit est poussé
Oui, c’est tout à fait possible.

Un workflow peut envoyer automatiquement un message lorsqu’un commit est poussé sur une branche :

- “Nouveau commit sur `main`”
- “Quelqu’un vient de pousser du code”
- “Attention : commit sur une branche sensible”

Cela permet de suivre l’activité du dépôt sans devoir surveiller GitHub en permanence (on a plus le nez autre part que sur Git).

---

## ✔️ 3. Lancer automatiquement des tests
En production, c’est un usage essentiel.

À chaque commit ou pull request, un workflow peut :

- lancer les tests unitaires ;
- vérifier que le code compile ;
- analyser la qualité du code.

Si quelque chose échoue, le workflow peut envoyer un message sur Discord pour prévenir l’équipe.

---

## ✔️ 4. Déployer automatiquement une application
C’est l’un des usages les plus courants.

Un workflow peut :

- envoyer les fichiers sur un serveur ;
- mettre à jour un site web ;
- redémarrer un service ;
- publier une nouvelle version d’une API.

Le tout sans intervention humaine.

---

## ✔️ 5. Générer automatiquement des fichiers
Par exemple :

- générer une documentation ;
- créer un rapport ;
- produire un build front-end ;
- compiler un binaire.

Ensuite, le workflow peut envoyer un message Discord pour dire :  
“Votre build est prêt.”

---

## ✔️ 6. Nettoyer ou organiser automatiquement le projet
Un workflow peut aussi servir à :

- supprimer des fichiers temporaires ;
- formater automatiquement le code ;
- vérifier que les conventions sont respectées ;
- analyser les dépendances.

---

## ✔️ 7. Surveiller l’état du projet
Avec `schedule`, un workflow peut s’exécuter automatiquement à intervalles réguliers.

Exemples :

- vérifier chaque matin que le site est en ligne ;
- tester une API toutes les heures ;
- envoyer un rapport quotidien sur Discord.

---

# En gros :

Un workflow en production sert à :

- automatiser ;
- sécuriser ;
- surveiller ;
- déployer ;
- notifier.

Et dans le contexte de la formation :

- **Le webhook Discord est un excellent moyen de visualiser ce que fait un workflow.**  
- **Vous pouvez l’utiliser pour notifier un commit, un test, un échec, un succès, un déploiement, etc.**