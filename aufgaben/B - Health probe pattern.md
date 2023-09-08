[2_Unterrichtsressourcen/B · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/B)
#### [Hauptseite](/README.md)
#### [A - Jobs](/aufgaben/A%20-%20Jobs.md)
# B - Health probe pattern

## Liveness-Test
Das Kubelet verwendet Liveness Probes, um zu wissen, wann ein Container neu gestartet werden muss. Liveness Probes können zum Beispiel einen Deadlock erkennen, bei dem eine Anwendung zwar läuft, aber nicht vorankommt. Der Neustart eines Containers in einem solchen Zustand kann dazu beitragen, dass die Anwendung trotz Fehlern besser verfügbar ist.
Folgende möglichkeit für einen Liveness-Test gibt Kubernetes:
- [Liveness-Test via "command"](#liveness-test-via-command)
- [Liveness-Test via HTTP anfrage](#liveness-test-via-command)
- [Liveness-Test via TCP](#liveness-test-via-command)
- [Liveness-Test via gRPC](#liveness-test-via-command)

### Liveness-Test via "command"

Das folgende Skrip prüft mittels einem "command" ob der Container noch so funtkioniert wie definiert.
Beim Starten des Pods wird defniert, dass da directory mit dem File `/tmp/healty` erstellt wird. Nun defniert man bei der Liveness-Probe das es mittel "command", also `cat /tmp/healty` geprüft wird ob das File existiert. Existiert das File bleibt der Container am Leben und wenn nicht wird dieser gelöscht und neu Gestartet. Für die Prüfug gibt es verschiedene Parameter die man setzen kann, in diesem Beispiel verwenden wir den `initialDelaySeconds` (wie lange soll bis zur ersten Prüfung gewartet werden) und `periodSeconds`(in welchlem Interval soll geprüft werden). 

In den ersten 30 Sekunden der Lebensdauer des Containers gibt es eine Datei `/tmp/healthy`. Während der ersten 30 Sekunden gibt der Befehl `cat /tmp/healthy` also einen Erfolgscode zurück. Nach 30 Sekunden liefert cat /tmp/healthy einen Fehlercode.
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5 
```




### Liveness-Test via HTTP anfrage
Text
### Liveness-Test via TCP
Text
### Liveness-Test via gRPC
Text

### K8s Kluster (Recap)

Auf Haupt Kubernetes ausführen `microk8s add-node`

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



#### [C - Init container](/aufgaben/C%20-%20Init%20container.md)
#### [Hauptseite](/README.md)