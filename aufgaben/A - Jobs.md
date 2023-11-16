[2_Unterrichtsressourcen/A · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/A)

# CronJobs

CronJobs sind wiederkehrende Aufgaben, die automatisiert auf unixartigen Betriebssystemen wie Linux oder iOS/Mac OS X sowie auf Serverumgebungen ausgeführt werden können. Der sogenannte Cron-Daemon bildet die Grundlage für CronJobs. Zum Beispiel die Automatisierung von Aufgaben wie das Herunterladen von Dateien für Backups.

![CroneJobs](/aufgaben/attachements/Snag_1b50784.png)

# Anleitung

1. Job Code in ein .yaml File kopieren

Dieses Skript zählt von 0-100 und das zweite 0-1000.

Unser Code Beispiel:

```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: count-to-1000
spec:
  schedule: "*/1 * * * *" # Schedule to run every minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: count-to-1000
            image: busybox:1.28
            command: ["sh", "-c", "for i in {1..1000}; do echo $i; done"]
          restartPolicy: OnFailure
```

```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: count-to-100
spec:
  schedule: "*/1 * * * *" # Schedule to run every minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: count-to-100
            image: busybox:1.28
            command: ["sh", "-c", "for i in {1..100}; do echo $i; done"]
          restartPolicy: OnFailure
```

1. Auf Kubernetes Webkonsole verbinden [Kubernetes Dashboard](https://10.1.38.36:8443/#/job?namespace=default)

3. Yaml File per Webkonsole hochladen

![bild2](/attachements/2.png)

![bild3](/attachements/3.png)

4. Nun auf die Kubernetes Maschine per CLI verbinden

Starten des Jobs:

```
kubectl apply -f https://k8s.io/examples/application/job/cronjob.yaml`
```

Anzeigen der Starts des Jobs, beenden mit Ctrl+c

```
kubectl get jobs --watch
```

Aufräumen

```
kubectl delete -f https://k8s.io/examples/application/job/cronjob.yaml
```

Hier aufgezeigt, wie dies aussieht:

![bild3](/attachements/4.png)

#### [B - Health probe pattern](/aufgaben/B%20-%20Health%20probe%20pattern.md)
