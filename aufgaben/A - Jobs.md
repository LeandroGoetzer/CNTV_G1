[2_Unterrichtsressourcen/A · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/A)

# CronJobs

1. Job Code in ein .yaml File kopieren

![[cronejob.yaml]]

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

![[Pasted image 20230907184952.png]]

![[Pasted image 20230907185224.png]]

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

![[Pasted image 20230907184758.png]]