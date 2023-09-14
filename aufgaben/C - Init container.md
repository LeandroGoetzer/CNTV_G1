[2_Unterrichtsressourcen/C · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/C)
#### [Hauptseite](/README.md)
#### [B - Health probe pattern](/aufgaben/B%20-%20Health%20probe%20pattern.md)
# C - Init container

Ein Init Container, ist eigentlich nichts anders als ein Prerequisite check, welcher vor dem Ausführen des eigentlichen Containers ausgeführt wird. In den Init Containern können somit verschiedene Voreinstellungen geprüft werden. 

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
    initContainers:
    - name: init-container-ping
      image: alpine:latest
      command: ["sh", "-c", "apk update && apk add iputils && ping -c 5 janic.ch"]

```
Das oben dargestellte Cloud Init Skript startet den pod myapp-prod. 
Bevor dieser allerdings installiert wird, muss die Domäne janic.ch 5x gepinged werden können, dazu muss das Packet iputils installiert werden. 

Nach dem deployen des Yaml files, kann man überprüfen das der Pod erfolgreich erstellt wurde: 

![bild6](/attachements/6.png)






#### [D - Horizontal pod autoscaler](/aufgaben/D%20-%20Horizontal%20pod%20autoscaler.md)
#### [Hauptseite](/README.md)