# Formation Docker

## TP : Hello World

Création d'un simple Dockerfile en se basant sur l'image tutum/hello-world.

Comment utiliser ce projet ?

1. Construire l'image Docker

```sh
docker build --tag ntdtfr/hello-world .
```

2. Exécution du container

```sh
docker run -p 8080:80 ntdtfr/hello-world
```

Testez l'url : http://localhost:8080


Si Ok, on push l'image sur le dépôt Docker Hub

```sh
docker push ntdtfr/hello-world
```