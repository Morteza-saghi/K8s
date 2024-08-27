# Helm
Helm is a package manager for Kubernetes, designed to simplify the deployment and management of applications on Kubernetes clusters. It helps you define, install, and upgrade even complex Kubernetes applications using charts, which are packages of pre-configured Kubernetes resources.

## Three Big Concepts


### Chart
A Chart is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, or a Yum RPM file.

### Repository 
A Repository is the place where charts can be collected and shared. It's like Perl's CPAN archive or the Fedora Package Database, but for Kubernetes packages.

### Release
A Release is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same cluster. And each time it is installed, a new release is created. Consider a MySQL chart. If you want two databases running in your cluster, you can install that chart twice. Each one will have its own release, which will in turn have its own release name.


- Helm installs charts into Kubernetes, creating a new release for each installation. And to find new charts, you can search Helm chart repositories.


## Finding Charts

helm search hub searches the Artifact Hub, which lists helm charts from dozens of different repositories.

helm search repo searches the repositories that you have added to your local helm client (with helm repo add). This search is done over local data, and no public network connection is needed. 


## Installing a Package


To install a new package, use the ```helm install``` command. At its simplest, it takes two arguments: A release name that you pick, and the name of the chart you want to install.

#### Like :


```
helm install happy-panda bitnami/wordpress
```

---

#### To keep track of a release's state

```
helm status happy-panda
```

---


### Customizing the Chart Before Installing

Installing the way we have here will only use the default configuration options for this chart. Many times, you will want to customize the chart to use your preferred configuration.

To see what options are configurable on a chart, use ```helm show values```.

```
helm show values nextcloud/nextcloud --version 5.2.2
```


You can then override any of these settings in a YAML formatted file, and then pass that file during installation.

```
helm install -f values.yaml bitnami/wordpress --generate-name
```

### More Installation Methods


- A local chart archive (helm install foo foo-0.1.1.tgz)
- An unpacked chart directory (helm install foo path/to/foo)
- A full URL (helm install foo https://example.com/charts/foo-1.2.3.tgz)
