[2_Unterrichtsressourcen/I · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/I)
#### [Hauptseite](/README.md)
#### [H - Helm der paketmanager für Kubernetes](/aufgaben/H-%20Helm%20der%20paketmanager%20für%20Kubernetes.md)
# I - Operator Pattern

## Wie könnte ein Operator im Detail aussehen? Hier ist ein Beispiel:

1. Eine benutzerdefinierte Ressource namens SampleDB, die Sie im Cluster konfigurieren können.
2. Ein Deployment, das dafür sorgt, dass ein Pod läuft, der den Controller-Teil des Operators enthält.
3. Ein Container-Image des Operator-Codes.
4. Controller-Code, der die Kontrollebene abfragt, um herauszufinden, welche SampleDB-Ressourcen konfiguriert sind.
5. Der Kern des Operators ist der Code, der dem API-Server mitteilt, wie er die Realität mit den konfigurierten Ressourcen abgleichen soll.
- Wenn Sie eine neue SampleDB hinzufügen, richtet der Operator PersistentVolumeClaims ein, um dauerhaften Datenbankspeicher bereitzustellen, ein StatefulSet, um SampleDB auszuführen, und einen Job, um die anfängliche Konfiguration zu handhaben.
- Wenn Sie die Datenbank löschen, erstellt der Operator einen Snapshot und stellt sicher, dass StatefulSet und Volumes ebenfalls entfernt werden.
6. Der Operator verwaltet auch regelmäßige Datenbank-Backups. Für jede SampleDB-Ressource legt der Operator fest, wann ein Pod erstellt werden soll, der eine Verbindung zur Datenbank herstellen und Backups erstellen kann. Diese Pods stützen sich auf eine ConfigMap und/oder ein Secret, das die Verbindungsdaten und Anmeldeinformationen der Datenbank enthält.
7. Da der Betreiber eine robuste Automatisierung für die von ihm verwaltete Ressource anstrebt, würde es zusätzlichen unterstützenden Code geben. In diesem Beispiel prüft der Code, ob die Datenbank mit einer alten Version arbeitet, und erstellt in diesem Fall Auftragsobjekte, die ein Upgrade vornehmen.

## Using an operator

Alle nötigen Files erstellt:

        nano Docker
        nano pods-hooks.sh
        nano shell-operator-pod.yaml

[Dockerfile](./scripts/Dockerfile)
[pods-hooks.sh](./scripts/pods-hooks.sh)
[shell-operator-pod.yaml](./scripts/shell-operator-pod.yaml)

Dockerfile:

        FROM flant/shell-operator:latest
        ADD pods-hook.sh /hooks

pods-hook.sh:

        #!/usr/bin/env bash

        if [[ $1 == "--config" ]] ; then
        cat <<EOF
        configVersion: v1
        kubernetes:
        - apiVersion: v1
        kind: Pod
        executeHookOnEvent: ["Added"]
        EOF
        else
        podName=$(jq -r .[0].object.metadata.name $BINDING_CONTEXT_PATH)
        echo "Pod '${podName}' added"
        fi

shell-operator-pod.yaml:

        apiVersion: v1
        kind: Pod
        metadata:
        name: shell-operator
        spec:
        containers:
        - name: shell-operator
            image: registry.gitlab.com/ch-tbz-hf/stud/v-cnt/shell-operator
            imagePullPolicy: Always
        serviceAccountName: monitor-pods-acc

Docker installieren:

        sudo snap install docker

Docker Container erstellen names shell:

        docker build -t shell .

Pod exampl-monitor erstellen:

        kubectl create namespace example-monitor-pods
        kubectl create serviceaccount monitor-pods-acc --namespace example-monitor-pods
        kubectl create clusterrole monitor-pods --verb=get,watch,list --resource=pods
        kubectl create clusterrolebinding monitor-pods --clusterrole=monitor-pods --serviceaccount=example-monitor-pods:monitor-pods-acc
        kubectl -n example-monitor-pods apply -f shell-operator-pod.yaml


Nach dem Starten eines Pods, sollte das Log um ein paar Einträge reicher sein

        kubectl run hello-world --image registry.gitlab.com/mc-b/misegr/hello-world

        kubectl -n example-monitor-pods logs po/shell-operator

#### [J - Monitoring](/aufgaben/J%20-%20Monitoring.md)
#### [Hauptseite](/README.md)