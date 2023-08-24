# Kubernetes

# Goal

In this respostory we're discussing the overall Kubernetes archtectures along with some DevOps best practices, such as GitOps.

# Instalation of ArgoCD

The argocd will target the kafka folder in which all the manifests for kafka will be versioned.

The installation can be done with

> kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.5.8/manifests/install.yaml

I recommend using [Lens](https://k8slens.dev/) and portfowarding the service to your localhost. Enable this traffic on your anti-virus.

Then enter the UI with the outputed password from the following command.

> kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

Follow this (tutorial)[https://medium.com/@mehmetodabashi/installing-argocd-on-minikube-and-deploying-a-test-application-caa68ec55fbf#:~:text=To%20use%20Argo%20CD%20with,would%20with%20a%20production%20cluster.] for more details.

# Installing kafka

Once argocd is installed we're using gitops to make kafka availabe, no need to install workloads manually with kubectl.

Now, one configured the kafka folder for this respository on the minikube, add the

Download the bitnami helm chart and paste it on the folder for the ArgoCd app.

> https://github.com/bitnami/charts/tree/master/bitnami/kafka

To simplify, set in values.yaml 'persistence: false' to it runs gracefully. So argocd will get it up and running:

![image](https://github.com/keisyd/kubernets-fundamentals/assets/57769796/a6efb80a-da10-4295-b067-1ee02226a523)

Do some tests to see if it's working

> kubectl –namespace default exec -it kafka-0 – kafka-topics.sh –create –zookeeper kafka-zookeeper:2181 –replication-factor 1 –partitions 1 –topic test
> kubectl –namespace default exec -it kafka-0 – kafka-topics.sh –list –zookeeper kafka-zookeeper:2181

# Folders

## [archtecture](https://github.com/keisyd/kubernets-fundamentals/tree/main/archtecture)

In the archtecture folder some of the principles and fundamentals are explained about the kubernetes archtecture.

## [setup-minikube](https://github.com/keisyd/kubernets-fundamentals/tree/main/setup-minikube)

Here we have both a basic setup for the application on `basic` using only the console and kubectl cli.

Also we have the explanation on how to setup argocd to use GitOps
