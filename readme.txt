# 📌 Automatisation des Sauvegardes GitHub avec AWS CodeBuild et EventBridge

## 📖 Introduction
Dans un environnement DevOps, l'automatisation des sauvegardes est essentielle pour garantir la sécurité et la disponibilité des données.
Ce projet met en place une architecture event-driven qui automatise la sauvegarde des fichiers d’un dépôt GitHub sur Amazon S3 en utilisant
AWS CodeBuild et Amazon EventBridge.

## 🏗️ Architecture
L'architecture repose sur les services AWS suivants :
- **GitHub** : Dépôt source contenant les fichiers CloudFormation.
- **Amazon EventBridge** : Détecte les événements `push` du dépôt GitHub et déclenche CodeBuild.
- **AWS CodeBuild** : Clone le dépôt, archive les fichiers en `.zip` et les stocke dans Amazon S3.
- **Amazon S3** : Stocke les archives générées pour une récupération ultérieure.
- **Amazon CloudWatch** : Surveille les logs de l'exécution du processus.

## 🔧 Configuration
### 1️⃣ Prérequis
- Un compte AWS avec les permissions adéquates.
- Un dépôt GitHub contenant des fichiers CloudFormation.

### 2️⃣ Étapes d'installation
#### 1️⃣ Créer une connexion AWS CodeStar vers GitHub
1. Accéder à **AWS CodeStar connections**.
2. Ajouter une nouvelle connexion et sélectionner GitHub.
3. Autoriser AWS à accéder au dépôt GitHub.

#### 2️⃣ Créer un rôle IAM pour CodeBuild
1. Accéder à **IAM > Rôles**.
2. Créer un rôle avec les permissions suivantes :
   ```json
   {
     "Effect": "Allow",
     "Action": [
       "s3:PutObject",
       "s3:GetObject",
       "logs:CreateLogGroup",
       "logs:CreateLogStream",
       "logs:PutLogEvents"
     ],
     "Resource": "*"
   }
   ```

#### 3️⃣ Configurer AWS CodeBuild
1. Accéder à **AWS CodeBuild** et créer un projet.
2. Sélectionner la connexion CodeStar vers GitHub.
3. Définir le fichier `buildspec.yml`.

#### 4️⃣ Créer une règle EventBridge
1. Accéder à **Amazon EventBridge > Règles**.
2. Ajouter une règle avec l'événement GitHub `push`.
3. Définir CodeBuild comme cible.

### 3️⃣ Fichier `buildspec.yml`
```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
  build:
    commands:
      - echo "Clonage du dépôt GitHub"
      - git clone $CODEBUILD_SOURCE_REPO_URL repo
      - cd repo
      - zip -r ../backup.zip .
  post_build:
    commands:
      - echo "Upload vers S3"
      - aws s3 cp ../backup.zip s3://mon-bucket-backup/backup-$(date +%F-%H-%M-%S).zip
```

## 🚀 Test et Déploiement
1. Effectuer un `git push` sur le dépôt GitHub.
2. Vérifier que la règle EventBridge a bien déclenché CodeBuild.
3. Vérifier que le fichier `.zip` est bien stocké dans S3.
4. Consulter les logs dans Amazon CloudWatch.

## 🎯 Conclusion
Cette solution permet d'automatiser la sauvegarde des fichiers CloudFormation stockés sur GitHub vers S3 en utilisant une architecture event-driven.
Elle garantit une récupération rapide et sécurisée des fichiers en cas de besoin.

## 📌 Technologies utilisées
- AWS CodeBuild
- Amazon EventBridge
- Amazon S3
- Amazon CloudWatch
- GitHub


