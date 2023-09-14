[2_Unterrichtsressourcen/B · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/B)
#### [Hauptseite](/README.md)
#### [A - Jobs](/aufgaben/A%20-%20Jobs.md)
# B - Health probe pattern

## Liveness-Test
Das Kubelet verwendet Liveness Probes, um zu wissen, wann ein Container neu gestartet werden muss. Liveness Probes können zum Beispiel einen Deadlock erkennen, bei dem eine Anwendung zwar läuft, aber nicht vorankommt. Der Neustart eines Containers in einem solchen Zustand kann dazu beitragen, dass die Anwendung trotz Fehlern besser verfügbar ist.
Folgende möglichkeit für einen Liveness-Test gibt Kubernetes:
- [Liveness-Test via "command"](#liveness-test-via-command)
- [Liveness-Test via HTTP anfrage](#liveness-test-via-http-anfrage)
- [Liveness-Test via TCP](#liveness-test-via-tcp)

### Liveness-Test via "command"

In den ersten 30 Sekunden der Lebensdauer des Containers gibt es eine Datei `/tmp/healthy`. Während der ersten 30 Sekunden gibt der Befehl `cat /tmp/healthy` also einen Erfolgscode zurück. Nach 30 Sekunden liefert `cat /tmp/healthy` einen Fehlercode und der Container wird gelöscht und neu aufgebaut.
<strong>Code:</strong>
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
Eine Weiter Methode ist es mit "HTTP Request" zu prüfen ob der Container noch am Leben ist. 
Jeder HTTP-Statuscode, der größer oder gleich 200 und kleiner als 400 ist, bedeutet Erfolg. Jeder andere Code bedeutet einen Fehler. Der Quellcode für den Server ist in [Server.go](https://github.com/kubernetes/kubernetes/blob/master/test/images/agnhost/liveness/server.go) zu sehen.
In den ersten 10 Sekunden, in denen der Container aktiv ist, gibt der /healthz-Handler einen Status von 200 zurück. Danach gibt der Handler einen Status von 500 zurück.Danach wird der Container gelöscht.
<strong>Code:</strong>
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```

### Liveness-Test via TCP
Eine dritte Methode wäre es über TCP sich mit dem Container zu verbinden. Sollange die Verbindung erfolgreich ist bleibet der Container bestehend.
Die Konfiguration für die TCP-Prüfung ist der HTTP-Prüfung sehr ähnlich. In diesem Beispiel werden sowohl Readiness- als auch Liveness-Probes verwendet. Das Kubelet sendet die erste Readiness-Probe 5 Sekunden nach dem Start des Containers. Damit wird versucht, eine Verbindung zum `goproxy` Container auf Port 8080 herzustellen. Wenn die Prüfung erfolgreich ist, wird der Pod als bereit markiert. Das Kubelet wird diese Prüfung weiterhin alle 10 Sekunden durchführen.
<strong>Code:</strong>
```
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
  - name: goproxy
    image: registry.k8s.io/goproxy:0.1
    ports:
    - containerPort: 8080
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20

```
## Readiness
Manchmal sind Anwendungen vorübergehend nicht in der Lage, den Datenverkehr zu bedienen. Beispielsweise muss eine Anwendung während des Starts große Daten- oder Konfigurationsdateien laden oder ist nach dem Start auf externe Dienste angewiesen. In solchen Fällen möchten man die Anwendung nicht abschalten, aber auch keine Anfragen an sie senden. Kubernetes bietet Readiness-Probes, um solche Situationen zu erkennen und zu entschärfen. Ein Pod mit Containern, die melden, dass sie nicht bereit sind, erhält keinen Datenverkehr über Kubernetes-Dienste.
Readiness-Probes werden ähnlich wie Liveness-Probes konfiguriert. Der einzige Unterschied besteht darin, dass Sie das Feld `readinessProbe` anstelle des Feldes `livenessProbe` verwenden.
<strong>Code:</strong>
```
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

## Startup Probes
In manchen Fällen hat man mit Legacy-Anwendungen zu tun, die bei ihrer ersten Initialisierung eine zusätzliche Startzeit benötigen könnten. In solchen Fällen kann es knifflig sein, Parameter für eine Liveness-Probe einzurichten, ohne die schnelle Reaktion auf Deadlocks zu beeinträchtigen, die eine solche Probe motiviert. Der Trick besteht darin, eine Startup-Probe mit demselben Befehl, HTTP- oder TCP-Check, mit einem `failureThreshold * periodSeconds` einzurichten, der lang genug ist, um den schlimmsten Fall einer Startup-Zeit abzudecken.
<strong>Code Bsp.:</strong>
```
ports:
- name: liveness-port
  containerPort: 8080
  hostPort: 8080

livenessProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 1
  periodSeconds: 10

startupProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 30
  periodSeconds: 10
```


#### [C - Init container](/aufgaben/C%20-%20Init%20container.md)
#### [Hauptseite](/README.md)