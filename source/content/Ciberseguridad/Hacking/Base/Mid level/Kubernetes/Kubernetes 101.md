# Pods
En Kubernetes, un pod puede ser uno o varios contenedores que Kubernetes trata como una unidad, todos los contenedores dentro de un pod X serán programados, spawneados y terminados a la vez

Se interactúa con Kubernetes mediante los *manifest files*, archivos en los que se describe el estado deseado de la infraestructura (que pods correr, con que imágenes, como se comunican...)

![[pods.jpg|535x529]]

```yaml
# myapp.yaml file
# Minimal description to start a pod with 2 containers
apiVersion: v1
kind: Pod # We want to deploy a pod
metadata: name: myapp # Name of the pod labels: app: myapp # Label used
to search/select the pod
spec: containers: 
name: nginx # First container image:
sparcflow/nginx # Name of the public image ports: 
image: containerPort: 8080
# Listen on the pod's IP address 
name: mydb # Second container
image: redis # Name of the public image ports: 
containerPort: 6379
```

Por ejemplo, manifest file crea un pod llamado `app:myapp`, con esta arquitectura

![[manifestExample.jpg|334x365]]

El manifest se envía mediante `kubectl`, el programa con el que se interactúa con el cluster. El pod con ambos contenedores tiene la misma IP, y se pueden comunicar entre ellos a través de su localhost aislado mediante namespacing en `127.0.0.1`. En realidad kubernetes crea un tercer contenedor llamado pause-container, dueño de la red y el namespace que comparte con el resto de contenedores del pod.

Cada pod tiene su propia IP y existe dentro de una VM o bare-metal llamado nodo. Que a su vez puede existir dentro de otro host en caso de ser una VM.

![[nodes.jpg]]

Todos los pods y nodos dentro de un cluster pueden comunicarse entre sí sin necesidad de usar NAT, es decir, un pod A en una VM 1 puede comunicarse con pod B alojado en máquina 2.

# Balancear el tráfico 
