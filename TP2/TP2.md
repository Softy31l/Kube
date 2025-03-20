#TP2 Gestion Ingress

#1-Installer Kind et créer votre premier cluster

kube@kube:~/Kube/TP2$ kind create cluster --name kindfst
Creating cluster "kindfst" ...
 ✓ Ensuring node image (kindest/node:v1.32.2) 🖼
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kindfst"
You can now use your cluster with:

kubectl cluster-info --context kind-first-cluster

Thanks for using kind! 😊

#2-Installer 

kube@kube:~/Kube/TP2$ kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml

#3.4

Création d'un arborescence pour avoir un dockerfile et un index.html pour chaque magasin

	kube@kube:~/Kube/TP2$ tree
.
├── sites
│   ├── mesbonlegumesbio
│   │   ├── dockerfile
│   │   └── index.html
│   ├── mesbonslegumes
│   │   ├── dockerfile
│   │   └── index.html
│   └── monbonlait
│       ├── dockerfile
│       └── index.html
└── TP2.md

	Dockerfile
'kube@kube:~/Kube/TP2/sites/mesbonlegumesbio$ cat dockerfile
 
 - Utilisation de l'image officielle Nginx
FROM nginx:latest

 -  Suppression des fichiers HTML par défaut de Nginx
RUN rm -rf /usr/share/nginx/html/*

  - Copie du fichier index.html du magasin dans le répertoire de Nginx
COPY index.html /usr/share/nginx/html/index.html

  - Exposition du port 80 pour accéder au site web
EXPOSE 80'

	Index.html
'kube@kube:~/Kube/TP2/sites/mesbonlegumesbio$ cat index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Simple App</title>
  </head>
  <body>
    <h1>Bienvenue chez Mes bons legumes bio</h1>
        <p>
Ici on vend des bon legumes bio>
        </p>
  </body>
</html>'

#3.5 

├── sites
│   ├── ingress.yml (ingress a la racine)
│   ├── mesbonlegumesbio
│   │   ├── deployment.yml (un déploiement...
│   │   ├── dockerfile
│   │   ├── index.html
│   │   └── service.yml ...et un service par dossier de magasin)
│   ├── mesbonslegumes
│   │   ├── deployment.yaml
│   │   ├── dockerfile
│   │   ├── index.html
│   │   └── service.yaml
│   └── monbonlait
│       ├── deployment.yml
│       ├── dockerfile
│       ├── index.html
│       └── service.yml

	Ingress.yml
'apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: magasins-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: mesbonlegumesbio.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mesbonlegumesbio-service
            port:
              number: 80
  - host: mesbonslegumes.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mesbonslegumes-service
            port:
              number: 80
  - host: monbonlait.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: monbonlait-service
            port:
              number: 80 '


	Deployment.yml
'apiVersion: apps/v1
kind: Deployment
metadata:
  name: mesbonlegumesbio
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mesbonlegumesbio
  template:
    metadata:
      labels:
        app: mesbonlegumesbio
    spec:
      containers:
        - name: mesbonlegumesbio
          image: ynov31/nginx-mesbonlegumesbio:latest
          ports:
            - containerPort: 80'

	Service.yml
'apiVersion: v1
kind: Service
metadata:
  name: mesbonlegumesbio-service
spec:
  selector:
    app: mesbonlegumesbio
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP'

Application des yml créé avec les commandes correspondant aux magasins
'kubectl apply -f sites/[mesbonlegumesbio, ...]/deployment.yml
kubectl apply -f sites/[mesbonlegumesbio,...]/service.yml'

Puis déploiement de l'ingress
'kubectl apply -f ingress.yml'

	Vérification de la création des pods ...
'kube@kube:~/Kube/TP2$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
mesbonlegumesbio-759f576859-2zd55   1/1     Running   0          10m
mesbonslegumes-6c767f6664-vpcqw     1/1     Running   0          12m
monbonlait-84454556d8-lztmq         1/1     Running   0          10m
'
	des services ... 
'kube@kube:~/Kube/TP2$ kubectl get svc
NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes                 ClusterIP   10.96.0.1       <none>        443/TCP   172m
mesbonlegumesbio-service   ClusterIP   10.96.225.38    <none>        80/TCP    14m
mesbonslegumes-service     ClusterIP   10.96.100.222   <none>        80/TCP    15m
monbonlait-service         ClusterIP   10.96.112.225   <none>        80/TCP    13m
'
	de l'ingress
'kube@kube:~/Kube/TP2$ kubectl get ingress
NAME               CLASS    HOSTS                                                          ADDRESS     PORTS   AGE
magasins-ingress   <none>   mesbonlegumesbio.local,mesbonslegumes.local,monbonlait.local   localhost   80      13m
'

#3.6 

On peut gérer une augmentation de la charge en augmentant le nombre de réplica souhaités, ajustables sur le fichier de deploiement. Cela augmente le nombre de pods 

'apiVersion: apps/v1
kind: Deployment
metadata:
  name: mesbonlegumesbio
spec:
 ** replicas: 1 => X souhaités **
  selector:
    matchLabels:
      app: mesbonlegumesbio
'
Pour voir la répartition de la charge on peut effectuer la commandes suivantes:
'kubectl get endpoints monbonlait-service'
et afficher plusieurs addresse IP correspondant aux pods existant.

#3.7

#Bonus
