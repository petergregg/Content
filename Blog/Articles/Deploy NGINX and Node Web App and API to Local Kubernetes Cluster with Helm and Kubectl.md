---
title: Deploy NGINX Ingress Controller and Node.js Web App and API to Local Kubernetes Cluster with Helm and Kubectl
author: Peter Gregg
description: Deploy NGINX Ingress Controller and Node.js Web App and API to Local Kubernetes Cluster with Helm, Helm Charts and Kubectl
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2023/07/10 19:20:00
categories: 
  - Docker Desktop
  - WSL
  - Kubernetes Dashboard UI
  - Visual Studio Code
  - Kubernetes
  - Helm
  - Helm Charts
  - Kubectl
  - Nginx Ingress Controller
  - Node.js
---

# Deploy NGINX Ingress Controller and Node.js Web App and API to Local Kubernetes Cluster with Helm and Kubectl
In this article, you will learn how to deploy [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/) and a Node.js web app and api with a mongodb to a local Kubernetes Cluster with Helm and Kubectl. At the end of this tutorial you will be able to build and deploy containerized applications to local Kubernetes. This article was inspired by the [Microsoft Cloud Workshop Cloud Native Applications](https://github.com/microsoft/MCW-Cloud-native-applications). 

## Prerequisites
The following prerequisites will be required to complete this tutorial:
- Visual Studio Code. If you don't have Visual Studio Code installed, [download Visual Studio Code for free](https://code.visualstudio.com/Download) before you begin.
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop with Kubernetes enabled. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.
- Helm. If you don't have Helm installed, [install helm for free](https://helm.sh/docs/intro/install/) before you begin.

# Clone the MCW-Cloud-native-applications project
1. Open **Visual Studio Code**, select **Terminal**, and then **New Terminal**. Ensure you are running a Powershell terminal.

    ![Visual Studio Code Open New Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeOpenTerminal.png)

2. In the Terminal, navigate to a folder on your device where you would like to store a temporary project for this tutorial. Replace `{DevSample}` with your chosen folder.

    ```
    cd /DevSample
    ```

3. Run the following commands to create a new folder named **FabmedicalTemp** and navigate to the folder.

    ```
    mkdir FabmedicalTemp
    cd FabmedicalTemp
    ```

4. Run the following command to clone the **MCW-Cloud-native-applications** project repository.

    ```
    git clone https://github.com/microsoft/MCW-Cloud-native-applications.git
    ```
    
5. Run the following command to remove the **.git** directory because it is not required.

    ```
    rm MCW-Cloud-native-applications/.git -r -force
    ```

6. Run the the following command to navigate to the **developer** folder.

    ```
    cd MCW-Cloud-native-applications/'Hands-on lab'/lab-files/developer
    ```

7. Run the following commands to initialize a new git repository and to set your username and email, for git to use for commits. Replace `{YourEmail@example.com}` with your email and `{Your Name}` with your name.

    ```
    git init
    git config --global user.email "{YourEmail@example.com}"
    git config --global user.name "{Your Name}"
    git add .
    git commit -m "Initial Commit"
    ```

8. Navigate to a folder on your device where you would like to store the **Fabmedical Kubernetes** project for this tutorial. Replace `{DevSample}` with your chosen folder.

    ```
    cd /DevSample
    ```

9. Run the following commands to create a new folder named **FabmedicalK8s** and navigate to the folder.

    ```
    mkdir FabmedicalK8s
    cd FabmedicalK8s
    ```

10. Run the following command to clone the repository code into the **FabmedicalK8s** folder. Replace `{DevSample}` with your chosen folder.

    ```
    git clone C://DevSample/FabmedicalTemp/MCW-Cloud-native-applications/'Hands-on lab'/lab-files/developer .
    ```

# Push the FabmedicalK8s to a GitHub repo (Optional)

1. Create a GitHub repository to store the new project in your GitHub account named `Fabmedicalk8s`.

2. Run the following command to set the remote origin to the GitHub URL, replace `{YourGitHubUsername}` with your Github username.

    ```
    git remote add origin https://github.com/{YourGitHubUsername}/Fabmedicalk8s.git
    ```

3. Run the following command to push to the main branch.
    
    ```
    git push -u origin main
    ```

# Install Kubernetes Dashboard UI
1. In Visual Studio Code, select **Terminal**, and then **New Terminal**. Run the following command to deploy the Kubernetes Dashboard UI.

    ```
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
    ```

2. Run the following command to ensure the pods are running.

    ```
    kubectl get pods -A
    ```

3. Run the following commands to navigate to **Fabmedicalk8s** folder and create a new folder named **deploy**. Replace `{DevSample}` with your chosen folder.

    ```
    cd /DevSample/FabmedicalK8s
    mkdir deploy
    cd deploy
    ```

4. Run the following commands to create a new folder named **K8s** in the **deploy** folder and navigate to it.

    ```
    mkdir K8s
    cd K8s
    ```

5. Run the following command to add a new yaml file named `dashboard-adminuser` into the **K8s** folder. 

    ```
    code dashboard-adminuser.yaml
    ```

6. Add the following contents into the `dashboard-adminuser` yaml file.

    ```
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin-user
      namespace: kubernetes-dashboard
    ```

7. Run the following command to add a new yaml file named `dashboard-adminuser-authorization` into the **K8s** folder. 

    ```
    code dashboard-adminuser-authorization.yaml
    ```

8. Add the following contents into the `dashboard-adminuser-authorization` yaml file.

    ```
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

9. Save the changes and close the file.

10. Run the following commands to apply the configuration specified in the `dashboard-adminuser` and `dashboard-adminuser-authorization` yaml files.

    ```
    kubectl apply -f dashboard-adminuser.yaml
    kubectl apply -f dashboard-adminuser-authorization.yaml
    ```

11. Run the following command to create a token to log into the Kubernetes Dashboard UI. Copy the token for later.

    ```
    kubectl -n kubernetes-dashboard create token admin-user
    ```

12. Run the following command to start the Kubernetes Dashboard UI.

    ```
    kubectl proxy
    ```

13. Navigate to http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login. Select **Token** and paste the copied token into **Enter token** box.

    ![kubernetes Dashboard Login](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/KubernetesDashboard/KubernetesDashboardLogin.png)

> [!NOTE] 
> Every time you restart the cluster you have to manually start the dashboard and get a new login token. 

# Setup and deploy services using Helm and Helm Charts

## Install mongodb using Helm

1. Run the following `helm install` command to [install bitnami mongodbs image](https://github.com/bitnami/charts/tree/main/bitnami/mongodb).

    ```
    helm install my-release oci://registry-1.docker.io/bitnamicharts/mongodb --set auth.enabled=false
    ```

## Build a docker image and setup Helm chart to run the content initialization container

1. In the same Terminal, run the following commands to navigate to **content-init** folder and build the content initialization docker image.

    ```
    cd ../../content-init
    docker image build -t content-init .
    ```

2. Run the following commands to navigate to the **k8s** folder and create a folder named **helm**.

    ```
    cd ../deploy/k8s
    mkdir helm
    ```

3. Run the following commands to navigate to the **helm** folder and create the scaffolded helm charts for the content initialization project.

    ```
    cd helm
    helm create content-init
    ```

4. Run the following commands to navigate to the **content-init** folder and open the scaffolded `values` yaml file.

    ```
    cd content-init
    code values.yaml
    ```

5. Search for the `image` definition and update the values to match the following.

    ```
    image:
      repository: content-init
      pullPolicy: Never
    ```

6. Search for `nameOverride` and `fullnameOverride`, and update the values to match the following.

    ```
    nameOverride: "content-init"
    fullnameOverride: "content-init"
    ```

7. Save the changes and close the file.

8. Run the following command to open the scaffolded `chart` yaml file.

    ```
    code chart.yaml
    ```

9. Search for `appVersion` and update the values to match the following.

    ```
    appVersion: latest
    ```

10. Save the changes and close the file.

11. Run the following commands to navigate to the **templates** folder and open the scaffolded `deployment` yaml file.

    ```
    cd templates
    code deployment.yaml
    ```

12. Search for the `containers` definition and add the following `env` variable.

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

13. Save the changes and close the file.

14. Run the following command to navigate to the **helm** folder.

    ```
    cd ../../
    ```

15. Run the following `helm install` command.

    ```
    helm install content-init ./content-init
    ```

## Build a docker image and setup Helm chart to run the content api container

1. In the same Terminal, run the following commands to navigate to **content-api** folder and build the api docker image.

    ```
    cd ../../../content-api
    docker image build -t content-api .
    ```

2. Run the following commands to navigate to the **helm** folder and create the scaffolded helm charts for the api project.

    ```
    cd ../deploy/k8s/helm
    helm create api
    ```

3. Run the following commands to navigate to the **api** folder and open the scaffolded `values` yaml file.

    ```
    cd api
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

7. Search for the `resources` definition. Remove the curly braces and add `requests` to match the following.

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

8. Save changes and close the file.

9. Run the following command to open the scaffolded `chart` yaml file.

    ```
    code chart.yaml
    ```

10. Search for `appVersion` and update the values to match the following.

    ```
    appVersion: latest
    ```

11. Save changes and close the file.

12. Run the following commands to navigate to the **templates** folder and open the scaffolded `deployment` yaml file.

    ```
    cd templates
    code deployment.yaml
    ```

13. Search for the `metadata` definition and replace the line under `annotations` with the following.

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

14. Search for the `containers` definition and update the `containerPort`, and `livenessProbe port` values to match the following. Also add the following `env` variable.

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

15. Save the changes and close the file.

16. Run the following command to open the scaffolded `service` yaml file in the **templates** folder.

    ```
    code service.yaml
    ```

17. Search for the `ports` definition and update the values to match the following.

    ```
    ports:
      - port: {{ .Values.service.port }}
        targetPort: 3001
        protocol: TCP
        name: http
    ```
18. Save the changes and close the file.

19. Run the following command to navigate to the **helm** folder

    ```
    cd ../../
    ```

20. Run the following `helm install` command.

    ```
    helm install api ./api
    ```

## Build a docker image and setup Helm chart to run the content web container

1. In the same Terminal, run the following commands to navigate to **content-web** folder and build the web docker image.

    ```
    cd ../../../content-web
    docker image build -t content-web .
    ```

2. Run the following commands to navigate to **helm** and create the scaffolded helm charts for the web project.

    ```
    cd ../deploy/k8s/helm
    helm create web
    ```

3. Run the following commands to navigate to the **web** folder and open the scaffolded `values` yaml file.

    ```
    cd web
    code values.yaml
    ```

4. Search for the `image` definition and update the values to match the following.

    ```
    image:
      repository: content-web
      pullPolicy: Never
    ```

5. Search for `nameOverride` and `fullnameOverride`, and update the values to match the following.

    ```
    nameOverride: "web"
    fullnameOverride: "web"
    ```

6. Search for the `service` definition and update the values to match the following.

    ```
    service:
      type: LoadBalancer
      port: 3000
      targetPort: 3000
      protocol: TCP
      name: web
    ```

7. Search for the `resources` definition. Remove the curly braces and add `requests` to match the following.

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

8. Save the changes and close the file.

9. Run the following command to open the scaffolded `chart` yaml file in the helm **web** folder.

    ```
    code chart.yaml
    ```

10. Search for `appVersion` and update the values to match the following.

    ```
    appVersion: latest
    ```

11. Save the changes and close the file.

12. Run the following commands to navigate to the **templates** folder and open the scaffolded `deployment` yaml file.

    ```
    cd templates
    code deployment.yaml
    ```

13. Search for the `metadata` definition and replace the line under `annotations` with the following.

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

14. Search for the `containers` definition and update the `containerPort`, and `livenessProbe port` values to match the following. Also add the following `env` variable.

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

15. Save the changes and close the file.

16. Run the following command to open the scaffolded `service` yaml file in the **templates** folder.

    ```
    code service.yaml
    ```

17. Search for the `ports` definition and update the values to match the following.

    ```
    ports:
      - port: {{ .Values.service.port }}
        targetPort: 3000
        protocol: TCP
        name: http
    ```

18. Save the changes and close the file.

19. Run the following command to navigate to the **helm** folder.

    ```
    cd ../../
    ```

20. Run the following `helm install` command.

    ```
    helm install web ./web
    ```

21. Open the Kubernetes Management Dashboard UI and select **Services** under **Discovery and Load Balancing** in the navigation menu. 

    ![Kubernetes Dashboard Discovery And Load Balancing Services Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/KubernetesDashboard/KubernetesDashboardDiscoveryAndLoadBalancingServicesNavigation.png)

22. In the **Services** view, select the **web service**. Once the web service has finished deploying, select the external endpoint to access the website.

    ![Kubernetes Dashboard Discovery And Load Balancing Services](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/KubernetesDashboard/KubernetesDashboardDiscoveryAndLoadBalancingServices.png)

# Install NGINX using Helm

1. In the same Terminal, run the following command to navigate to the **k8s** folder in the **deploy** folder.

    ```
    cd ../
    ``` 

2. Run the following `helm install` command.

    ```
    helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace
    ```

3. Run the following commands to create a folder named **nginx-ingress** and navigate to the new **nginx-ingress** folder.

    ```
    mkdir nginx-ingress
    cd nginx-ingress
    ```

4. Run the following command to add a new yaml file named `content-nginx`. 

    ```
    code content-nginx.yaml
    ```

5. Add the following contents to the `content-nginx` yaml file.

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

6. Run the following command to apply the nginx configuration specified in the `content-nginx` yaml file to the `default` namespace.

    ```
    kubectl apply -f content-nginx.yaml --namespace default 
    ```

> [!NOTE] 
> If you have any other applications running on your device on port 80, stop them otherwise they will clash with the following.

7. Navigate to http://localhost, in the navigation, select **speakers** to view the speakers data and select **sessions** to view the sessions data.

    ![Fabmedical Website Home Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Fabmedical/FabmedicalWebsiteHomePage.png)

    ![Fabmedical Website Speakers Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Fabmedical/FabmedicalWebsiteSpeakersPage.png)

    ![Fabmedical Website Sessions Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Fabmedical/FabmedicalWebsiteSessionsPage.png)




