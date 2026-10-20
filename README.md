# TP DevOps Correction Docker

Correction de la partie Docker du module DevOps. Amusez-vous bien avec GitHub Actions !

This project is now mine

## TP part 02 – GitHub Actions

Pipeline CI/CD : à chaque push, le backend est testé et analysé par SonarCloud ; sur `main`, si les tests passent, les 3 images Docker sont publiées sur Docker Hub.

### Workflows (`.github/workflows/`)
| Fichier | Déclenchement | Rôle |
|---|---|---|
| `test-backend.yml` | push sur `main` / `develop`, pull request | `mvn clean verify` (tests unitaires + intégration) puis analyse SonarCloud |
| `build-and-push.yml` | `workflow_run` après *Test backend*, seulement sur `main` et si succès | Build et push des images `simple-api`, `database`, `httpd` sur Docker Hub |

### Secrets GitHub
`DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN` (token Read & Write), `SONAR_TOKEN`.

### SonarCloud
- Organisation `leticiagaspar`, projet `leticiagaspar_devops-cicd`, analyse automatique désactivée (l'analyse est faite par la pipeline).
- Le raccourci `sonar:sonar` ne fonctionnait pas (plugin non déclaré dans le `pom.xml`) : utilisation du nom complet `org.sonarsource.scanner.maven:sonar-maven-plugin:sonar`.
- `sonar.token` à la place de `sonar.login` (déprécié).

### Questions

**2-1 Que sont les testcontainers ?**
Des librairies Java qui lancent des containers Docker pendant les tests. Ici, un vrai PostgreSQL (URL `jdbc:tc:postgresql`) pour les tests d'intégration, supprimé à la fin.

**2-2 Pourquoi des variables sécurisées ?**
Pour utiliser les identifiants (Docker Hub, SonarCloud) dans la pipeline sans les exposer dans le code : GitHub les chiffre et les masque dans les logs.

**2-3 Pourquoi `needs: test-backend` ?**
Sans lui, les jobs tournent en parallèle et une image pourrait être publiée même si les tests échouent. Avec `needs` (puis `workflow_run` + condition `success`), on publie seulement si les tests passent.

**2-4 Pourquoi pousser les images ?**
Pour rendre la version testée disponible dans un registry, d'où les serveurs peuvent la récupérer et la déployer sans rebuild.
