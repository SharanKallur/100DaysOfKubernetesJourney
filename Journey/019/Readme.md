# Learning more about Helm | How to create a chart to install my demo application

## Introduction

One of the items on my to-do list, is to look more into using Helm. So far, I have used helm to install existing applications into Kubernetes, and provide answers to value files, to configure additional parameters outside of the default values. 

To understand more about helm, I decided to delve in by creating a chart, which is a package of YAML files for the deployment of components into a Kubernetes environment. 

I focused on creating this chart for my [Pac-Man Demo](https://github.com/pacman-tanzu) application that I've used for quite a while now.
## Prerequisite

Install helm ([official documentation](https://helm.sh/docs/intro/install/))
````
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh
````

Have an application or set of Kubernetes deployment files you want to create a helm chart for.
## Use Case

Using a helm package makes the deployment and configuration of application into different environments easier. It has also become one of the standard package delivery mechnasims when working with Kubernetes. So understanding Helm at a Level 150/200 will benefit you a lot. 
## Research

Official Docs 
– [Charts Template Guide](https://helm.sh/docs/chart_template_guide/getting_started/)
- [Control Structure](https://helm.sh/docs/chart_template_guide/control_structures/)
- [Template Functions and Pipelines](https://helm.sh/docs/chart_template_guide/functions_and_pipelines)
    - [Using the Lookup Function](https://helm.sh/docs/chart_template_guide/functions_and_pipelines/#using-the-lookup-function)
- [Charts Tricks and Tips](https://helm.sh/docs/howto/charts_tips_and_tricks/)

YouTube videos 
- from mkdev
    - [Let's Write Our First Helm Chart!](https://www.youtube.com/watch?v=xHqnilCYufE)
    - [Helm Templates and Values: Make Re-usable Helm Charts](https://www.youtube.com/watch?v=BGM3X59biFI)
    - [Helm Chart Dependencies and ArtifactHub](https://www.youtube.com/watch?v=ROq1CDuGYzg)
- from debug.school
    - Kubernetes With HELM Tutorial - Crash Course For Beginners 
        - [Part 1](https://www.youtube.com/watch?v=ctGAOiCTZl0)
        - [Part 2](https://www.youtube.com/watch?v=yG4oZ6NseAs)
        - [Part 3](https://www.youtube.com/watch?v=4D1FOq30Hkw)
        - [Part 4](https://www.youtube.com/watch?v=DyEwc-WGohQ)
- from TechWorld with Nana
    - [What is Helm in Kubernetes? Helm and Helm Charts explained | Kubernetes Tutorial 23](https://www.youtube.com/watch?v=-ykwb1d0DXU)
- from Anais Urlichs
    - [Full Tutorial: Deploying Helm Charts in Kubernetes with Terraform](https://www.youtube.com/watch?v=Qq1cfVw1Mx4)

Blogs
- Bitnami - [How to create your first helm chart](https://docs.bitnami.com/tutorials/create-your-first-helm-chart/)
- pheonixNAP - [How To Create A Helm Chart](https://phoenixnap.com/kb/create-helm-chart)
- Banzaicloud - [Helm from basics to advanced - Part 2](https://banzaicloud.com/blog/creating-helm-charts-part-2/)
- Tech Blog Posts by David Field - [From Zero to Code: Helm](https://tech.davidfield.co.uk/from-zero-to-code-enhancing-the-kubernetes-experience/)

Community
- Helm Chart Examples
    - GitHub User Sankalp-r - [_helpers.tpl file](https://github.com/sankalp-r/helm-charts-examples/blob/main/sample_chart/templates/_helpers.tpl)
    - GitHub User Helm - [Hello World Template Chart](https://github.com/helm/examples/blob/main/charts/hello-world/templates/deployment.yaml)
    - [A simple example for a helm chart](https://medium.com/htc-research-engineering-blog/a-simple-example-for-helm-chart-fbb5c7208e94)
        - This is based on an older version of Helm and references the use of Tiler which is now removed. However the concepts and examples are still pretty useful, especialy for the control language.
- Stackoverflow - [How to use lookup function](https://stackoverflow.com/questions/63443100/how-to-use-lookup-function-in-helm-chart)

Tools
- [KubeLinter](https://github.com/stackrox/kube-linter)
    - KubeLinter analyzes Kubernetes YAML files and Helm charts, and checks them against a variety of best practices, with a focus on production readiness and security.
## Try yourself

Here is the blog post I created on the subject taking you through how I created a chart with examples.
- [Creating and hosting a Helm Chart package to install Pac-Man on Kubernetes](https://veducate.co.uk/how-to-create-helm-chart/)

Below is a high level overview:

- Create a template chart

````
helm create {chart name}
````
- Fill out the charts.yaml file with your necessary information about the chart itself and application it will deploy
- Within your templates folder, add in your various YAML files needed to deploy your assets.
- Alter your YAML files with the jinja formatting and Helm Control Structure to make your deployment flexible.

````
# Providing a default value if one is not specified in the values file
storage: {{ .Values.mongo.storage | default "1Gi" }}

# Example to follow from the above breakdown
{{ ( printf .Values.mongo.databaseAdminName | b64enc ) | default "Y2x5ZGU=" }}

# Full example from the secret.yaml file

data:
  database-admin-name: {{ ( printf .Values.mongo.databaseAdminName | b64enc ) | default "Y2x5ZGU=" }}
  database-admin-password: {{ ( printf .Values.mongo.databaseAdminPassword | b64enc ) | default "Y2x5ZGU=" }}
  database-name: {{ ( printf .Values.mongo.databaseName | b64enc ) | default "cGFjbWFu" }}
  database-password: {{ ( printf .Values.mongo.databasePassword | b64enc ) | default "cGlua3k=" }}
  database-user: {{ ( printf .Values.mongo.databaseUser | b64enc ) | default "Ymxpbmt5" }}
````

- Edit the values.yaml file to provide the necessary information, either as defaults or user provided. Values in this file will be passed through the control structure when deploying the helm chart.
- Validate and test your chart deployment in your environment, the lint command will validate the syntax in your files for any obvious errors. The install with dry run argument will output the chart in use with the values specified, so you can validate the control structure works as expected. 

````
helm lint {chart location}

helm install {release name} --dry-run --debug {chart location} --create-namespace
````
- Package your helm chart
````
helm package {package_name}
````