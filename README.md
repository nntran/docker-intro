# Formation Docker

## TP : Hello World

Objectif : Créer un simple Dockerfile en se basant sur l'image tutum/hello-world.

Comment utiliser ce projet ?

1. Construire l'image Docker

```sh
docker build --tag ntdtfr/hello-world .
```

2. Exécuter le container

```sh
docker run -p 8080:80 ntdtfr/hello-world
```

3. Tester l'url : http://localhost:8080

4. Si Ok, on push l'image sur le dépôt Docker Hub

```sh
docker push ntdtfr/hello-world
```
