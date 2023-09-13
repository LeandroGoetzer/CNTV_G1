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

In den ersten 30 Sekunden der Lebensdauer des Containers gibt es eine Datei `/tmp/healthy`. Während der ersten 30 Sekunden gibt der Befehl `cat /tmp/healthy` also einen Erfolgscode zurück. Nach 30 Sekunden liefert `cat /tmp/healthy` einen Fehlercode.
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
Eine Weiter Methode ist es mit "HTTP Request" zu prüfen ob der Container noch am Leben ist. Sobal dieser keine Antwort gibt wird er Eliminiert.
Folgendes Beispiel:
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
Eine dritte Methode wäre es über TCP sich mit dem Container zu verbinden. Sollange die Verbindung erfolgreich ist bleibet der Container bestehend

### Liveness-Test via gRPC




#### [C - Init container](/aufgaben/C%20-%20Init%20container.md)
#### [Hauptseite](/README.md)