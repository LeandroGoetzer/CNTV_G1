#### [Hauptseite](/README.md)
#### [B - Health probe pattern](/aufgaben/B%20-%20Health%20probe%20pattern.md)
# K8s Cluster
Für das erstellen eines Klaster muss auf dem "Haupt" Kubernetes Maschiene der Befehel `microk8s add-node` ausgeführt werden.
Nun bekomme man die Join Instruction:

Join Instruction werden zurückgegeben für erster Node:
```
ubuntu@Kubernetes-36:~$ microk8s add-node
From the node you wish to join to this cluster, run the following:
microk8s join 10.0.41.72:25000/e80c4181fc4ce9bc5f3dcc085537d1a7/133792a3ad2f

Use the '--worker' flag to join a node as a worker not running the control plane, eg:
microk8s join 10.0.41.72:25000/e80c4181fc4ce9bc5f3dcc085537d1a7/133792a3ad2f --worker

If the node you are adding is not reachable through the default interface you can use one of the following:
microk8s join 10.0.41.72:25000/e80c4181fc4ce9bc5f3dcc085537d1a7/133792a3ad2f
microk8s join 10.1.38.36:25000/e80c4181fc4ce9bc5f3dcc085537d1a7/133792a3ad2f
```
Für jeden weitern Node zum hinufügen, muss der Befehl erneut ausgeführt werden.
Join Instruction werden zurückgegeben für zweiter Node:
```
ubuntu@Kubernetes-36:~$ microk8s add-node
From the node you wish to join to this cluster, run the following:
microk8s join 10.0.41.72:25000/84a54509d73cadd32df89b45fbfb74cf/133792a3ad2f

Use the '--worker' flag to join a node as a worker not running the control plane, eg:
microk8s join 10.0.41.72:25000/84a54509d73cadd32df89b45fbfb74cf/133792a3ad2f --worker

If the node you are adding is not reachable through the default interface you can use one of the following:
microk8s join 10.0.41.72:25000/84a54509d73cadd32df89b45fbfb74cf/133792a3ad2f
microk8s join 10.1.38.36:25000/84a54509d73cadd32df89b45fbfb74cf/133792a3ad2f
```
Join vorgang prüfen `microk8s kubectl get no`
Ausgabe:
```ubuntu@Kubernetes-36:~$ microk8s kubectl get no
NAME                STATUS   ROLES    AGE     VERSION
kubernetesnode-37   Ready    <none>   4m16s   v1.27.5
kubernetesnode-38   Ready    <none>   116s    v1.27.5
kubernetes-36       Ready    <none>   14d     v1.27.5
```

#### [Hauptseite](/README.md)