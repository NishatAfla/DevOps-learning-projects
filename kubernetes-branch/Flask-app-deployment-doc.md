Deploy multiple Flask applications on Kubernetes using Helm chart-

first we used NodePort service. previous task: https://dev.azure.com/fronturetech/Fronture%20ERP/_wiki/wikis/Fronture-ERP.wiki/349/Helm



**Nodeport Limitations**
- Each app requires a unique port.

- Not scalable for many apps.

- No path-based routing.

now we are migrating to Ingress for better routing and scalability.
_**Ingress Service with Multiple Apps**_

Now we will-
- Deploy two Flask apps using a single Helm chart.

- Use Ingress for routing based on URL paths.

- Manage everything using variables in values.yaml.

**Structure**

**values.yaml:** https://dev.azure.com/fronturetech/Fronture%20ERP/_git/hands-on-lab-nishat?path=/flask-helloApp-chart/values.yaml

apps:
- name: hello1
image: flask-hello1:h1
containerPort: 5000
path: /hello1

- name: hello2
image: flask-hello2:h2
containerPort: 5000
path: /hello2

**Key Templates**

**deployment.yaml:** https://dev.azure.com/fronturetech/Fronture%20ERP/_git/hands-on-lab-nishat?path=/flask-helloApp-chart/templates/deployment.yaml

{{- range .Values.apps }} //loop through multiple app
apiVersion: apps/v1
kind: Deployment
metadata:
name: {{ .name }}
spec:
replicas: 1
selector:
matchLabels:
app: {{ .name }}
template:
metadata:
labels:
app: {{ .name }}
spec:
containers:
- name: {{ .name }}
image: {{ .image }}
ports:
- containerPort: {{ .containerPort }}


{{- end }}

**service.yaml:** https://dev.azure.com/fronturetech/Fronture%20ERP/_git/hands-on-lab-nishat?path=/flask-helloApp-chart/templates/service.yaml

{{- range .Values.apps }}  //loop through multiple app
apiVersion: v1
kind: Service
metadata:
name: {{ .name }}
spec:
selector:
app: {{ .name }}
ports:
- protocol: TCP
port: 80
targetPort: {{ .containerPort }}

{{- end }}

**ingress.yaml:** https://dev.azure.com/fronturetech/Fronture%20ERP/_git/hands-on-lab-nishat?path=/flask-helloApp-chart/templates/ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: flask-ingress
spec:
rules:
- http:
paths:
{{- range .Values.apps }}
- path: {{ .path }}
pathType: Prefix
backend:
service:
name: {{ .name }}
port:
number: 80
{{- end }}

**_Now Apps Access Method_**

**Enable ingress:**

>minikube addons enable ingress

Our customized host name: hello-app.local
minikube Ip : 192.168.59.100
for setting customized host name :
Add the hosts entry:
echo "192.168.59.100 hello-app.local" | sudo tee -a /etc/hosts
![image.png](/.attachments/image-5c011462-55e2-40cc-abe1-e49a6f3943f8.png)

**Remember to add this host name at value.yaml file** 
>ingress: 
   host: "hello-app-local.dev"

**Then access via:**

http://hello-app.local/hello1
http://hello-app.local/hello2
![image.png](/.attachments/image-c566b9d0-5e58-406c-ad25-f78a73fffeba.png)


**Advantages**

- One Ingress handles multiple apps.

- Clean path-based routing.

- Easier to scale and manage.



