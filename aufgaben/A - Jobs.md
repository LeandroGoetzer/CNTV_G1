[2_Unterrichtsressourcen/A · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/A)

# CronJobs

1. Job Code in ein .yaml File kopieren

Code Beispiel:

```
apiVersion: batch/v1
kind: CronJob
metadata:
name: hello
spec:
schedule: "* * * * *"
jobTemplate:
    spec:
    template:
        spec:
        containers:
        - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
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