### Create Helm Chart for Microservices

### Technologies Used:

Kubernetes, Helm

### Project Description:

1- Create 1 shared Helm Chart for all microservices, to reuse common Deployment and Service configurations for the services

### Instructions:

###### Step 1: Create a helm chart structure

```
helm create microservices
```

###### Step 2: Create two helm chart template : deployment and service

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.appName }}
spec:
  replicas: {{ .Values.appReplicas }}
  selector:
    matchLabels:
      app: {{ .Values.appName }}
  template:
    metadata:
      labels:
        app: {{ .Values.appName }}
    spec:
      containers:
      - name: {{ .Values.appName }}
        image: "{{ .Values.appImage }}:{{ .Values.appVersion }}"
        ports:
        - containerPort: {{ .Values.containerPort }}
        env:
        {{- range .Values.containerEnvVars}}
        - name: {{ .name }}
          value: {{ .value | quote }}
        {{- end}}

```

```

258 bytes
apiVersion: v1
kind: Service
metadata:
  name: {{ .Values.appName }}
spec:
  type: {{ .Values.serviceType }}
  selector:
    app: {{ .Values.appName }}
  ports:
  - protocol: TCP
    port: {{ .Values.servicePort }}
    targetPort: {{ .Values.containerPort }}
```

###### Step 3: Create a default value for microservices

```
appName: servicename
appImage: gcr.io/google-samples/microservices-demo/servicename
appVersion: v.0.0.0
appReplicas: 1
containerPort: 8080
containerEnvVars:
- name: ENV_VAR_ONE
  value: "valueone"
- name: ENV_VAR_TWO
  value: "valuetwo"

servicePort: 8080
serviceType: ClusterIP

```

###### Step 4: Create two helm chart templates for redis: deployment and service

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: { { .Values.appName } }
spec:
  replicas: { { .Values.appReplicas } }
  selector:
    matchLabels:
      app: { { .Values.appName } }
  template:
    metadata:
      labels:
        app: { { .Values.appName } }
    spec:
      containers:
        - name: { { .Values.appName } }
          image: "{{ .Values.appImage }}:{{ .Values.appVersion }}"
          ports:
            - containerPort: { { .Values.containerPort } }
          volumeMounts:
            - name: { { .Values.volumeName } }
              mountPath: { { .Values.containerMountPath } }
      volumes:
        - name: { { .Values.volumeName } }
          emptyDir: {}
```

```
apiVersion: v1
kind: Service
metadata:
  name: { { .Values.appName } }
spec:
  type: ClusterIP
  selector:
    app: { { .Values.appName } }
  ports:
    - protocol: TCP
      port: { { .Values.servicePort } }
      targetPort: { { .Values.containerPort } }
```

###### Step 5: Create a default value for redis

```
appName: redis
appImage: redis
appVersion: alpine
appReplicas: 1
containerPort: 6379
volumeName: redis-data
containerMountPath: /data

servicePort: 6379
```
