

### Project Structure 

```
demo
├── Chart.yaml
├── production-values.yaml
├── staging-values.yaml
├── templates
│
├── deployment.yaml
│
└── service.yaml
└── values.yaml
```


#### The Chart.yaml file
it contains a file named Chart.yaml, which specifies the chart name and version:

```
name: demo
sources:
- https://github.com/cloudnativedevops/demo
version: 1.0.1
```

There are lots of optional fields you can supply in Chart.yaml, including a link to the project source code, as here, but the only required information is the name and version.


---


### The values.yaml file
There is also a file named values.yaml, which contains user-modifiable settings that
the chart author has exposed:

```
environment: development
container:
name: demo
port: 8888
image: cloudnatived/demo
tag: hello
replicas: 1
```

This looks a bit like a Kubernetes YAML manifest, but there’s an important difference. The values.yaml file is completely free-form YAML, with no predefined schema: it’sup to you to choose what variables are defined, their names, and their values. There don’t have to be any variables at all in your Helm chart, but if there are, you can put them in values.yaml and then refer to them elsewhere in the chart. Ignore the production-values.yaml and staging-values.yaml files for the moment; we’ll explain what they’re for shortly.


### Helm Templates
So where are these variables referenced? If you look in the templates subdirectory, you’ll see a couple of familiar-looking files:
```
ls k8s/demo/templates
deployment.yaml service.yaml
```
These are just the same as the Deployment and Service manifest files from the
previous example, except that now they are templates: instead of referring to things
like the container name directly, they contain a placeholder that Helm will replace
with the actual value from values.yaml.


Here’s what the template Deployment looks like:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.container.name }}-{{ .Values.environment }}
spec:
  replicas: {{ .Values.replicas }}
  selector:
    matchLabels:
      app: {{ .Values.container.name }}
  template:
    metadata:
      labels:
        app: {{ .Values.container.name }}
        environment: {{ .Values.environment }}
    spec:
      containers:
        - name: {{ .Values.container.name }}
          image: {{ .Values.container.image }}:{{ .Values.container.tag }}
          ports:
            - containerPort: {{ .Values.container.port }}
          env:
            - name: ENVIRONMENT
              value: {{ .Values.environment }}

```



### Interpolating Variables

There are several variables referenced in this template:  

```
metadata:
  name: {{ .Values.container.name }}-{{ .Values.environment }}
```
This whole section of text, including the curly braces, will be interpolated (that is, replaced) with the values of container.name and environment, taken from values.yaml. The generated result will look like this:
```
metadata:
  name: demo-development
```

This is powerful because values like container.name are referenced more than once in the template. Naturally, it’s referenced in the Service template, too:
ی
```
apiVersion: v1
kind: Service
metadata:
  name: {{ .Values.container.name }}-service-{{ .Values.environment }}
  labels:
    app: {{ .Values.container.name }}
spec:
  ports:
    - port: {{ .Values.container.port }}
      protocol: TCP
      targetPort: {{ .Values.container.port }}
  selector:
    app: {{ .Values.container.name }}
  type: ClusterIP
```

You can see how many times .Values.container.name is referenced, for example. Even in a simple chart like this, you need to repeat the same bits of information many times.

Using Helm variables eliminates this duplication. All you need to do to change the container name, for example, is to edit values.yaml and reinstall the chart, and the change will be propagated throughout all the templates.

The Go template format is very powerful, and you can use it to do much more than simple variable substitutions: it supports loops, expressions, conditionals, and even calling functions. Helm charts can use these features to generate fairly complex configurations from input values, unlike the simple substitutions in our example.

