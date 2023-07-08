---
title: Deploy NGINX and Node Web App and API to Local Kubernetes Cluster with Helm and Kubectl
author: Peter Gregg
description: Deploy NGINX and Node Web App and API to Local Kubernetes Cluster with Helm and Kubectl
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/07/07 19:00:00
categories: 
  - Docker Desktop
  - WSL
  - Kubernetes Dashboard UI
  - Visual Studio Code
  - Kubernetes
  - Helm
  - Kubectl
  - Nginx
---

# Deploy NGINX and Node Web App and API to Local Kubernetes Cluster with Helm and Kubectl
In this article, you will learn how to deploy NGINX and a node web app and api with a mongodb to a Local Kubernetes Cluster with Helm and Kubectl.

## Prerequisites
The following prerequisites will be required to complete this tutorial:
- Visual Studio Code
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.

# Clone the Fabmedical project
1. Open Visual Studio Code, select **Terminal**, and then **New Terminal**. 
2. Create a new folder named **Fabmedical** and navigate into the folder.

    ```
    mkdir Fabmedical
    cd Fabmedical
    ```

3. Clone the **Fabmedical** project repository with the following command.

    ```
    git clone https://github.com/microsoft/MCW-Cloud-native-applications.git
    ```
    
4. Remove the **.git** directory because it is not required.

    ```
    rm -rf MCW-Cloud-native-applications/.git
    ```

5. Navigate to the **developer** folder.

    ```
    cd 'Hands-on lab'/lab-files/developer
    ```

# Install Kubernetes Dashboard UI
1. Open Visual Studio Code, select **Terminal**, and then **New Terminal**.vDeploy the Kubernetes Dashboard UI with the following command.

    ```
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
    ```

2. Ensure the pods are running with the following command.

    ```
    kubectl get pods -A
    ```

3. Create a new folder named **deploy** in the **root** of the **Fabmedical** project.

    ```
    mkdir deploy
    cd deploy
    ```


4. Create a new folder named **K8s** in the **deploy** folder with the following command.

    ```
    mkdir K8s
    cd K8s
    ```

5. Add a yaml file named `dashboard-adminuser` into the **K8s** folder. 

    ```
    > dashboard-adminuser.yaml
    ```

6. Add the following contents into the `dashboard-adminuser` yaml file.

    ```
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin-user
      namespace: kubernetes-dashboard
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: admin-user
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: cluster-admin
    subjects:
    - kind: ServiceAccount
      name: admin-user
      namespace: kubernetes-dashboard
    ```

7. In Visual Studio Code, right click on the **K8s** folder and select **Open in Integrated Terminal**. Run the following command.

    ```
    kubectl apply -f dashboard-adminuser.yaml
    ```

8. Create a token to log into the Kubernetes Dashboard UI with the following command, and copy.

    ```
    kubectl -n kubernetes-dashboard create token admin-user
    ```

9. Run the Kubernetes Dashboard UI with the following command.
    ```
    kubectl proxy
    ```

10. Navigate to http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login. Select **Token** and paste the copied token into **Enter token** box.

    ![kubernetes Dashboard Login](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/KubernetesDashboard/KubernetesDashboardLogin.png)

    !IMPORTANT You have to manually start the dashboard and get a new login token every time you restart the cluster.

# Setup and deploy services using Helm charts

## Install mongodb using a Helm chart

1. Run the following helm installation command to [install bitnami mongodbs image](https://github.com/bitnami/charts/tree/main/bitnami/mongodb).

    ```
    helm install my-release oci://registry-1.docker.io/bitnamicharts/mongodb --set auth.enabled=false
    ```

## Build a docker image and setup Helm chart to run the content initialization container

1. Navigate to **content-init** folder in the **root** folder of the **Fabmedical** project and build the content initialization docker image using the following command.

    ```
    cd content-init
    docker image build -t content-init .
    ```

2. Create a folder named **helm** in the **k8s** folder which is located in the **deploy** folder.

    ```
    cd deploy/k8s
    mkdir helm
    cd helm
    ```

2. Navigate to the **helm** folder and create scaffolded helm charts for the content initialization project with the following command.

    ```
    cd helm
    helm create content-init
    ```

3. In the same terminal navigate to the **content-init** folder and open the scaffolded `values` yaml file, with the following command.

    ```
    cd  content-init
    code values.yaml
    ```

4. Search for the `image` definition and update the values to match the following.

    ```
    image:
      repository: content-init
      pullPolicy: Never
    ```

5. Search for `nameOverride` and `fullnameOverride`, and update the values to match the following.

    ```
    nameOverride: "content-init"
    fullnameOverride: "content-init"
    ```

6. Save the changes and close the file.

7. In VS Code navigate to the **content-init** folder and open the scaffolded `chart` yaml file.

8. Search for `appVersion` and update the values to match the following.

    ```
    appVersion: latest
    ```

12. Save the changes and close the file.

13. In a terminal navigate to the **templates** folder in the **content-init** folder and open the scaffolded `deployments` yaml file, with the following command.

    ```
    cd  content-init/templates
    code deployments.yaml
    ```

14. Search for the `containers` definition and add the following `env` variable.

    ```
    containers:
      ...
        ports:
          ...
        env:
          - name: MONGODB_CONNECTION
            value: mongodb://my-release-mongodb:27017/contentdb
        ...
    ```

15. Save the changes and close the file.

16. In Visual Studio Code, right click on the **helm** folder in the **k8s** folder which is located in the **deploy** folder and select **Open in Integrated Terminal**.

17. Run the following helm install command.

    ```
    helm install content-init ./content-init
    ```

## Build a docker image and setup Helm chart to run the content api container

1. Navigate to **content-api** folder in the **root** folder of the **Fabmedical** project and build the api docker image with the following command.

    ```
    cd content-api
    docker image build -t content-api .
    ```

2. Navigate to **helm** folder in the **k8s** folder which is located in the **deploy** folder and create the scaffolded helm charts for the api with the following command.

    ```
    cd ../deploy/k8s/helm
    helm create api
    ```

3. In the same Terminal, navigate to the **api** folder and open the scaffolded `values` yaml file.

    ```
    cd  api
    code values.yaml
    ```

4. Search for the `image` definition and update the values to match the following.

    ```
    image:
      repository: content-api
      pullPolicy: Never
    ```

5. Search for `nameOverride` and `fullnameOverride`, and update the values to match the following.

    ```
    nameOverride: "api"
    fullnameOverride: "api"
    ```

6. Search for the `service` definition and update the values to match the following.

    ```
    service:
      type: LoadBalancer
      port: 80
    ```

7. Search for the `resources` definition. Remove the curly braces and add requests to match the following.

    ```
    resources:
      # We usually recommend not to specify default resources and to leave this as a conscious
      # choice for the user. This also increases chances charts run on environments with little
      # resources, such as Minikube. If you do want to specify resources, uncomment the following
      # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
      # limits:
      #  cpu: 100m
      #  memory: 128Mi
      requests:
        cpu: 1000m
        memory: 128Mi
    ```

9. Save changes and close the file.

10.  In the helm **api** folder, open the scaffolded `chart` yaml file.

    ```
    code chart.yaml
    ```

11. Search for `appVersion` and update the values to match the following.

    ```
    appVersion: latest
    ```

12. Save changes and close the file.

13. In a Terminal, navigate to the **templates** folder in the **api** folder and open the scaffolded `deployments` yaml file.

    ```
    cd  api/templates
    code deployments.yaml
    ```

14. Search for the `metadata` definition and replace the the line under annotations with the following.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      (...)
    spec:
      (...)
      template:
        metadata:
          (...)
          annotations:
            rollme: {{ randAlphaNum 5 | quote }}
    ```

15. Search for the `containers` definition and update the `containerPort`, `livenessProbe port` to match the following. Also add the following `env` variable.

```
containers:
  ...
    ports:
      ...
        containerPort: 3001
        ...
    env:
      - name: MONGODB_CONNECTION
        value: mongodb://my-release-mongodb:27017/contentdb
    livenessProbe:
      httpGet:
        ...
        port: 3001
```

16. Save the changes and close the file.

17. Open the scaffolded `services` yaml file in the **templates** folder.

    ```
    code services.yaml
    ```

18. Search for the `ports` definition and update the values to match the following.

    ```
    ports:
      - port: {{ .Values.service.port }}
        targetPort: 3001
        protocol: TCP
        name: http
    ```
19. Save the changes and close the file.

20. In Visual Studio Code, right click on the **helm** folder in the **k8s** folder which is located in the **deploy** folder and select **Open in Integrated Terminal**.

21. Run the following helm install command.

    ```
    helm install api ./api
    ```

## Build a docker image and setup Helm chart to run the content web container

1. Navigate to **content-web** folder in the **root** folder of the **Fabmedical** project and build the content-web docker image with the following command.

    ```
    cd content-web
    docker image build -t content-web .
    ```

2. Navigate to **helm** folder in the **k8s** folder which is located in the **deploy** folder and create the scaffolded helm charts for the web with the following command.

    ```
    cd ../deploy/k8s/helm
    helm create web
    ```

3. In the same Terminal navigate to the **web** folder and open the scaffolded `values` yaml file.

    ```
    cd  web
    code values.yaml
    ```

5. Search for the `image` definition and update the values to match the following.

    ```
    image:
      repository: content-web
      pullPolicy: Never
    ```

6. Search for `nameOverride` and `fullnameOverride`, and update the values to match the following.

    ```
    nameOverride: "web"
    fullnameOverride: "web"
    ```

7. Search for the `service` definition and update the values to match the following.

      ```
      service:
        type: LoadBalancer
        port: 3000
        targetPort: 3000
        protocol: TCP
        name: web
      ```

8. Search for the `resources` definition. Remove the curly braces and add requests to match the following.

    ```
    resources:
      # We usually recommend not to specify default resources and to leave this as a conscious
      # choice for the user. This also increases chances charts run on environments with little
      # resources, such as Minikube. If you do want to specify resources, uncomment the following
      # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
      # limits:
      #  cpu: 100m
      #  memory: 128Mi
      requests:
        cpu: 1000m
        memory: 128Mi
    ```

9. Save the changes and close the file.

10. In the same Terminal open the scaffolded `chart` yaml file in the helm web folder.

    ```
    code chart.yaml
    ```

11. Search for `appVersion` and update the values to match the following.

    ```
    appVersion: latest
    ```

12. Save the changes and close the file.

13. In the same Terminal navigate to the **templates** folder in the helm **web** folder and open the scaffolded `deployments` yaml file.

    ```
    cd  templates
    code deployments.yaml
    ```

14. Search for the `metadata` definition and replace the the line under annotations with the following.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      (...)
    spec:
      (...)
      template:
        metadata:
          (...)
          annotations:
            rollme: {{ randAlphaNum 5 | quote }}
    ```

15. 15. Search for the `containers` definition and update the `containerPort`, `livenessProbe port` to match the following. Also add the following `env` variable.

    ```
    containers:
      ...
        ports:
          ...
            containerPort: 3000
            ...
        env:
          - name: CONTENT_API_URL
            value: http://api:3001
        livenessProbe:
          httpGet:
            ...
            port: 3000
    ```

16. Save the changes and close the file.

17. In the same Terminal open the scaffolded `services` yaml file in the web **templates** folder.

    ```
    code services.yaml
    ```

18. Search for the `ports` definition and update the values to match the following.

    ```
    ports:
      - port: {{ .Values.service.port }}
        targetPort: 3000
        protocol: TCP
        name: http
    ```

19. Save the changes and close the file.

20. In Visual Studio Code, right click on the **helm** folder in the **k8s** folder which is located in the **deploy** folder and select **Open in Integrated Terminal**.

21. Run the following helm install command.

    ```
    helm install web ./web
    ```

22. Open the Kubernetes management dashboard ui and select **Services** under **Discovery and Load Balancing** in the navigation menu. In the **Services** view, select the **web service**. 

    ![Kubernetes Dashboard Discovery And Load Balancing Services Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/KubernetesDashboard/KubernetesDashboardDiscoveryAndLoadBalancingServicesNavigation.png)

23. Once the web service has finished deploying, select the external endpoint to access the website.

    ![Kubernetes Dashboard Discovery And Load Balancing Services](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/KubernetesDashboard/KubernetesDashboardDiscoveryAndLoadBalancingServices.png)

# Install NGINX using a Helm chart

1. Run the following helm install command.

    ```
    helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace
    ```

2. Navigate to the **k8s** folder in the **deploy** folder in the **root** folder of the **Fabmedical** project and create a folder named **nginx-ingress**, then navigate to the new **nginx-ingress** folder.

    ```
    mkdir nginx-ingress
    cd nginx-ingress
    ```

3. Create a new yaml file named `content-nginx` with the following contents.

    ```
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: content-ingress
      annotations:
        nginx.ingress.kubernetes.io/ssl-redirect: "false"
        nginx.ingress.kubernetes.io/use-regex: "true"
        nginx.ingress.kubernetes.io/rewrite-target: /$1
    spec:
      ingressClassName: nginx
      rules:
      - http:
          paths:
          - path: /(.*)
            pathType: Prefix
            backend:
              service:
                name: web
                port:
                  number: 3000
          - path: /api/(.*)
            pathType: Prefix
            backend:
              service: 
                name: api
                port: 
                  number: 3001
    ```

4. Apply the nginx config specified in the `content-nginx` yaml file to the `default` namespace with the following command.

    ```
    kubectl apply -f content-nginx.yaml --namespace default 
    ```

5. Navigate to http://localhost, in the navigation, select **speakers** to view speakers data and select **sessions** to view sessions data.

    ![Fabmedical Website Home Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Fabmedical/FabmedicalWebsiteHomePage.png)

    ![Fabmedical Website Speakers Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Fabmedical/FabmedicalWebsiteSpeakersPage.png)

    ![Fabmedical Website Sessions Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Fabmedical/FabmedicalWebsiteSessionsPage.png)




