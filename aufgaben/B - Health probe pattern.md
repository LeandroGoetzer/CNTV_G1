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
- [Liveness-Test via gRPC](#liveness-test-via-grpc)

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
Der Code für die Prüfung via Http ist im Image enthalten.
Code (Mehr Infos auf [Server.go](https://github.com/kubernetes/kubernetes/blob/master/test/images/agnhost/liveness/server.go)):

```
http.HandleFunc("/healthz", func(w http.ResponseWriter, r *http.Request) {
    duration := time.Now().Sub(started)
    if duration.Seconds() > 10 {
        w.WriteHeader(500)
        w.Write([]byte(fmt.Sprintf("error: %v", duration.Seconds())))
    } else {
        w.WriteHeader(200)
        w.Write([]byte("ok"))
    }
})
```

### Liveness-Test via TCP
Eine dritte Methode wäre es über TCP sich mit dem Container zu verbinden. Sollange die Verbindung erfolgreich ist bleibet der Container bestehend
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

### Liveness-Test via gRPC

```
apiVersion: v1
kind: Pod
metadata:
  name: etcd-with-grpc
spec:
  containers:
  - name: etcd
    image: registry.k8s.io/etcd:3.5.1-0
    command: [ "/usr/local/bin/etcd", "--data-dir",  "/var/lib/etcd", "--listen-client-urls", "http://0.0.0.0:2379", "--advertise-client-urls", "http://127.0.0.1:2379", "--log-level", "debug"]
    ports:
    - containerPort: 2379
    livenessProbe:
      grpc:
        port: 2379
      initialDelaySeconds: 10

```


#### [C - Init container](/aufgaben/C%20-%20Init%20container.md)
#### [Hauptseite](/README.md)