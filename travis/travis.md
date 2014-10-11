# Travis et l'integration continue

** Comment intégrer son projet Github et Travis CI ?

## Intégration continue avec un projet Nodejs (package npm...)

1. S'inscrire sur [https://travis-ci.org/auth](https://travis-ci.org/auth) avec con compte github

2. Aller sur la page profile [https://travis-ci.org/profile](https://travis-ci.org/profile) et active le projet github

3. Créér le fichier `.travis.yml` à la racine de son projet.

4. Configurer le fichier avec le language pour le test et les versions de Nodejs

```
language: node_js
node_js:
  - 0.10.32
  - 0.10
```

L'image du status se trouve à l'adresse suivante :

```
https://travis-ci.org/[YOUR_GITHUB_USERNAME]/[YOUR_PROJECT_NAME].png
```
