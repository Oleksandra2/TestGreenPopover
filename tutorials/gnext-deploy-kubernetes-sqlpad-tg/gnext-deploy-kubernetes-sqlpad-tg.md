---
title: Play with the SAP HANA database on Google Kubernetes cluster Test Green Pop-over Thirty Four
description: Deploy SAP HANA and a web SQL client on Google Kubernetes Engine
primary_tag: products>sap-hana\,-express-edition
tags: [  tutorial>beginner, topic>big-data, topic>cloud, products>sap-hana, products>sap-hana\,-express-edition ]
---

## Prerequisites  
 - **Proficiency:** Beginner

## Details
### You will learn  
- How to deploy SAP HANA, express edition on Google Kubernetes Engine
- How to connect to the database and perform advanced analytics

### Time to Complete
**15 Min**

---

[ACCORDION-BEGIN [Step 1: ](Create and configure a new Kubernetes cluster)]
Use the credentials provided by one of the experts at the booth at Google Next '18 to log into the [Google Cloud Platform console](https://console.cloud.google.com).

![Create a new project](project.png)

Once there, use the menu on the top left corner to navigate a new Kubernetes cluster from the `Kubernetes Engine` menu.

![Create a new project](new.png)

Click **Create cluster**.

![Create a new project](new2.png)

Fill in the name, choose version `1.9.6` and click **Customize**.

![k8s details](details1.png)

Set the CPU to 2 and memory to 13. Choose **Ubuntu** as the operating system and set the size of the cluster to **1**.

![k8s details](node3.png)

Scroll down and click **Create**.

[ACCORDION-END]


[ACCORDION-BEGIN [Step 2: ](Connect to your cluster)]

The setup will take some time. Meanwhile, here is some information about what is SAP HANA, express edition to make waiting easier.

<iframe width="560" height="315" src="https://www.youtube.com/embed/xUWqP8wWJIQ" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Once the cluster has finished deploying, click **Connect**.

![Connect to cluster](connect.png)

Use **Run in Cloud Shell** to connect from the web console.

![Connect to cluster](connect2.png)

This will open a `gcloud` console with a command ready to connect to the cluster in your project and selected zone. Hit **enter** to execute the command.

![Connect to cluster](connect3.png)

[ACCORDION-END]

[ACCORDION-BEGIN [Step 3: ](Create the deployment configuration files)]

Go back to the `gcloud` console for your cluster.

![Connect to the cluster](gcloud.png)

Create a secret to fetch the Docker image using the command below. **Copy and paste** the command into the console and press **enter**.

```ssh
kubectl create secret docker-registry docker-secret --docker-server=https://index.docker.io/v1/ --docker-username=gnext18 --docker-password=SAP@Google --docker-email=hrocks@gmail.com
```

![Create Docker secret](secret.png)

From the console, use the following commands to create a new file and open the web text editor:

```
touch hxe.yaml
edit hxe.yaml
```

![Create Docker secret](edit.png)

Confirm opening an editor:

![Create Docker secret](conf.png)

This will open an online editor where you will paste the contents of the file below.


```text
kind: ConfigMap
apiVersion: v1
metadata:
  creationTimestamp: 2018-01-18T19:14:38Z
  name: hxe-pass
data:
  password.json: |+
    {"master_password" : "HXEHana1"}
---
kind: PersistentVolume
apiVersion: v1
metadata:
  name: persistent-vol-hxe
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 150Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/data/hxe_pv"
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: hxe-pvc
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hxe
  labels:
    name: hxe
spec:
  selector:
    matchLabels:
      run: hxe
      app: hxe
      role: master
      tier: backend
  replicas: 1
  template:
    metadata:
      labels:
        run: hxe
        app: hxe
        role: master
        tier: backend
    spec:
      initContainers:
        - name: install
          image: busybox
          command: [ 'sh', '-c', 'chown 12000:79 /hana/mounts' ]
          volumeMounts:
            - name: hxe-data
              mountPath: /hana/mounts
      volumes:
        - name: hxe-data
          persistentVolumeClaim:
             claimName: hxe-pvc
        - name: hxe-config
          configMap:
             name: hxe-pass
      imagePullSecrets:
      - name: docker-secret
      containers:
      - name: hxe-container
        image: "store/saplabs/hanaexpress:2.00.030.00.20180403.2"
        ports:
          - containerPort: 39013
            name: port1
          - containerPort: 39015
            name: port2
          - containerPort: 39017
            name: port3
          - containerPort: 8090
            name: port4
          - containerPort: 39041
            name: port5
          - containerPort: 59013
            name: port6
        args: [ "--agree-to-sap-license", "--dont-check-system", "--passwords-url", "file:///hana/hxeconfig/password.json" ]
        volumeMounts:
          - name: hxe-data
            mountPath: /hana/mounts
          - name: hxe-config
            mountPath: /hana/hxeconfig
      - name: sqlpad-container
        image: "sqlpad/sqlpad"
        ports:
        - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: hxe-connect
  labels:
    app: hxe
spec:
  type: LoadBalancer
  ports:
  - port: 39013
    targetPort: 39013
    name: port1
  - port: 39015
    targetPort: 39015
    name: port2
  - port: 39017
    targetPort: 39017
    name: port3
  - port: 39041
    targetPort: 39041
    name: port5
  selector:
    app: hxe
---
apiVersion: v1
kind: Service
metadata:
  name: sqlpad
  labels:
    app: hxe
spec:
  type: LoadBalancer
  ports:
  - port: 3000
    targetPort: 3000
    protocol: TCP
    name: sqlpad
  selector:
    app: hxe
```

The file will be automatically saved to your home directory (`/home/YOUR_GOOGLE_ID`).

[ACCORDION-END]

[ACCORDION-BEGIN [Step 4: ](Deploy a HANA Express Docker container on your Docker pod)]

Use the following command to deploy the image on your pods and check deployment:

```
kubectl create -f hxe.yaml
kubectl describe pods
```

Initially, you will probably see a message stating that the image is being pulled. Repeat the command a couple of minutes later until you see the container is started:

![Container is started](started_c.png)  

Use the following command to check the name of the pod. Make sure the status is `Running` and `2/2`.

```text
kubectl get pods
```

Use the name of the pod to replace it in the following command

```text
kubectl exec -it <<pod-name>> bash
```
For example:

![Container is started](ctl.png)  

Use the following SQL commands to connect to the database and enable document store for later user

```SQL
hdbsql -i 90 -d systemdb -u SYSTEM -p HXEHana1
```

Paste the following command into the SQL prompt

```sql
alter database HXE add 'docstore';
```

Use `quit` to exit the SQL command line and `exit`

![SQL command](2.png)  


[ACCORDION-END]

