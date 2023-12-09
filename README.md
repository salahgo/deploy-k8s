Dans ce TP nous allons déployer une application `rancher-demo` mais cette fois en utilisant `kubectl apply -f`.

*   Changez de contexte pour minikube avec `kubectl config use-context minikube`
*   Chargez également la configuration de minikube dans `Lens` en cliquant à nouveau sur plus et en selectionnant `minikube`
*   Créez un dossier `TP_deploy_k8s` sur le bureau de la machine distante et ouvrez le avec `VSCode`.

Nous allons d’abord déployer notre application comme un simple **Pod**

*   Créez un fichier `rancher-demo-pod.yaml` avec à l’intérieur le code d’exemple du cours précédent de la partie Pods.
*   Téléchargez l'image rancher-demo `docker pull salahgo/rancher-demo`
*   Appliquez le ficher avec `kubectl apply -f <fichier>`
*   Constatez dans Lens dans la partie pods que les deux conteneurs du pod sont bien démarrés (deux petits carrés vert à droite de la ligne du pod)
*   Modifiez le nom du pod dans la description précédente et réappliquez la configuration. Kubernetes mets à jour le nom.
*   Modifier le nom du conteneur `rancher-demo` et réappliquez la configuration. Que ce passe-t-il ?

\=> Kubernetes refuse d’appliquer le nouveau nom de conteneur car un pod est largement immutable. Pour changer d’une quelquonque façon les conteneurs du pod il faut supprimer (`kubectl delete -f <fichier>`) et recréer le pod. Mais ce travail de mise à jour devrais être géré par un déploiement pour automatiser et pour garantir la haute disponibilité de notre application `rancher-demo`.

*   Supprimez le pod.

Avec un déploiement (méthode à utiliser)
----------------------------------------

*   Créez un fichier `rancher-demo-deploy.yaml` avec à l’intérieur le code suivant à compléter:
  ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: rancher-demo
      labels:
        app: rancher-demo
    spec:
      selector:
        matchLabels:
          app: rancher-demo
      strategy:
        type: Recreate
      replicas: 1
      template:
        metadata:
          labels:
            app: rancher-demo
        spec:
          containers:
          - image: <image>
            name: <name>
            ports:
            - containerPort: <port>
              name: demo-http
  ```    

*   Appliquez ce nouvel objet avec kubectl.
*   Changez le nom d’un conteneur et réappliquez: Cette fois le déploiement se charge créer un nouveau pod avec les bonnes caractéristiques et de supprimer l’ancien.
*   Changez le nombre de réplicats.

Ajoutons un service en mode NodePort
------------------------------------

*   Créez un fichier `rancher-demo-svc.yaml` avec à l’intérieur le code suivant à compléter:
  ```
    apiVersion: v1
    kind: Service
    metadata:
      name: rancher-demo-service
      labels:
        app: rancher-demo
    spec:
      ports:
        - port: <port>
      selector:
        app: <app_selector> 
      type: NodePort
  ```
  
*   Appliquez ce nouvel objet avec kubectl.
*   Inspectez le service dans Lens.
*   Visitez votre application avec `minikube service rancher-demo-service`, le nombre de réplicat devrait apparaître.
*   Changez les labels du template et du selector dans le **déploiement** (2 fois `app: rancher-demo` à remplacer dans le fichier ) et supprimez le déploiement et réappliquez.
*   Constatez que l’application n’est plus accessible dans le navigateur. Pourquoi ?
*   Ajoutez un service LoadBalancer à la place de NodePort.

Déploiement de l'Application de TP3 dans Minikube
------------------------------------------

Création d'un Déploiement Kubernetes

*   Créez un fichier YAML pour le déploiement de votre application de TP3 "express-sqlite-app".
*   Définissez les spécifications nécessaires, y compris l'image Docker de votre application, le nombre de répliques, et les ports (utilisez les objets précédents pour ça).

Exposition de l'Application via un Service NodePort

*   Créez un fichier YAML pour le service NodePort.
*   Configurez les ports et le type de service pour exposer votre application.

Exposition via un LoadBalancer

*   Modifiez le type de service en LoadBalancer dans votre fichier YAML.
*   Utilisez minikube tunnel pour exposer le LoadBalancer avec `minikube tunnel`
