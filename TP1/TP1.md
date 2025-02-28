# Pod Nginx

## a. Héberger un premier Pod Nginx

Création fichier nginx.yml avec les entrées suivantes:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

## b. A l'aide de la commande kubectl port-forward et d'un navigateur accéder à la page par défaut de votre pod Nginx

Saisie de la commande suivante:
```bash
kubectl port-forward pod/nginx 8080:80
```

Utilisation de curl pour tester la page nginx:
```bash
kube@kube:~$ curl http://localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>
<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/> Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
kube@kube:~$
```

#Connexion entre plusieurs Pods

##a. A l’image du TP 1 sur Docker, héberger deux pods phpmyadmin et mysql,
cette fois-ci en utilisant minikube
Pod PHP
```
apiVersion: v1
kind: Pod
metadata:
  name: phpmyadmin
  labels:
    app: php
spec:
  containers:
  - name: phpmyadmin
    image: phpmyadmin:latest

    env:
      - name: PMA_HOST
        value: mysql-service

    ports:
      - containerPort: 80
```
Pod MYSQL
```
apiVersion: v1
kind: Pod
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  containers:
  - name: mysql
    image: mysql:latest

    env:
     - name: MYSQL_USER
       value: kube
     - name: MYSQL_PASSWORD
       value: kube*
     - name:  MYSQL_DATABASE
       value: kube
     - name: MYSQL_ROOT_PASSWORD
       value: root

    ports
     - containerPort: 3306
```
##b. Créer un service associé au Pod mysql
Service MYSQL
```
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
  labels:
    app: mysql
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
```
##c. Connecter phpmyadmin avec le Service mysql
On connecte le pod phpmyadmin grace au PMA_HOST dans lequel on vient renseigner le label du service Mysql

##d. Avec la commande kubectl-port forward, vérifier que phpmyadmin arrive à
contacter et administrer votre base de données mysql
```
$ kubectl port-forward pod/phpmyadmin 8080:80
```
![TP1_Kube_PHP](https://github.com/user-attachments/assets/2a41998d-3a91-4752-b592-021be83e9dcf)



