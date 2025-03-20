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
kube@kube:~/Kube/TP2/sites/mesbonlegumesbio$ cat dockerfile
 
 - Utilisation de l'image officielle Nginx
FROM nginx:latest

 -  Suppression des fichiers HTML par défaut de Nginx
RUN rm -rf /usr/share/nginx/html/*

  - Copie du fichier index.html du magasin dans le répertoire de Nginx
COPY index.html /usr/share/nginx/html/index.html

  - Exposition du port 80 pour accéder au site web
EXPOSE 80

	Index.html
kube@kube:~/Kube/TP2/sites/mesbonlegumesbio$ cat index.html
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
</html>
