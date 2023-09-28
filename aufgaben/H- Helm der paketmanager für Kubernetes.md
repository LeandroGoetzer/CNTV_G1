[2_Unterrichtsressourcen/H · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/H)
#### [Hauptseite](/README.md)
#### [E - Zugriffsteuerung](/aufgaben/E%20-%20Zugriffsteuerung.md)
# H - Helm der paketmanager für Kubernetes

In Kubernetes ist ein "Helm" ein Paketmanager, der dazu verwendet wird, Kubernetes-Anwendungen zu verwalten und zu bereitstellen. Helm ermöglicht es Entwicklern und Betreibern, Container-Anwendungen in Kubernetes-Clustern einfacher zu verwalten, indem es die Verpackung von Anwendungen und deren Abhängigkeiten in sogenannten "Charts" erleichtert.

Hier sind einige der wichtigsten Konzepte in Helm:

Chart: Ein Helm-Chart ist ein Paket von vordefinierten Kubernetes-Ressourcen, die eine Anwendung oder einen Service beschreiben. Ein Chart kann beispielsweise Konfigurationsdateien, Dienste, Berechtigungen und Abhängigkeiten enthalten. Mit Charts können Sie Anwendungen schnell und wiederholbar bereitstellen.

Release: Eine Helm-Release ist eine bestimmte Instanz eines Charts in einem Kubernetes-Cluster. Jedes Mal, wenn Sie ein Chart in einem Cluster bereitstellen, wird eine Release-Instanz erstellt. Diese Instanz kann unabhängig von anderen Instanzen des gleichen Charts konfiguriert und verwaltet werden.

Repository: Helm-Charts werden normalerweise in Repositories gespeichert, die als zentrale Orte dienen, um Charts zu veröffentlichen und gemeinsam zu nutzen. Helm-Repositories können öffentlich sein, oder Sie können eigene private Repositories erstellen, um proprietäre Charts zu hosten.

Values: In Helm können Sie sogenannte "Values" verwenden, um die Konfiguration eines Charts anzupassen. Values sind Parameter, die in einem Helm-Release verwendet werden, um Einstellungen wie Ressourcenlimits, Datenbankverbindungen oder andere anwendungsspezifische Konfigurationen anzupassen.

Template Engine: Helm verwendet eine Go-Vorlagen-Engine, um die in einem Chart definierten Kubernetes-Ressourcen mit den angegebenen Values zu kombinieren und YAML-Dateien zu generieren, die dann im Cluster bereitgestellt werden.

Durch die Verwendung von Helm können Entwickler und Betreiber Kubernetes-Anwendungen effizienter und konsistenter bereitstellen, aktualisieren und verwalten, wodurch die Komplexität der Kubernetes-Verwaltung reduziert wird. Helm bietet auch die Möglichkeit, Charts zu versionieren und auf einfache Weise auf neue Versionen zu aktualisieren. Dies macht es zu einem wichtigen Werkzeug in der Kubernetes-Entwicklung und -Betriebspraxis.

## Installation



Helm verfügt jetzt über ein Installationsskript, das automatisch die neueste Version von Helm abruft und lokal installiert.

Sie können dieses Skript abrufen und es dann lokal ausführen. Es ist gut dokumentiert, so dass Sie es durchlesen und verstehen können, was es tut, bevor Sie es ausführen.

        $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
        $ chmod 700 get_helm.sh
        $ ./get_helm.sh

![Bild](/attachements/8.png)

[Installationsanleitung](https://helm.sh/docs/intro/install/)


## Initialize a Helm Chart Repository

Sobald man Helm bereit hat, kann man ein Diagramm-Repository hinzufügen.

        $ helm repo add bitnami https://charts.bitnami.com/bitnami

Sobald diese installiert ist, kann man die charts auflisten, die man installieren kann:

        $ helm search repo bitnami

[QuickstartGuide](https://helm.sh/docs/intro/quickstart/)

![Bild](/attachements/9.png)

## Install an Example Chart

Um eine chart zu installieren, kann man den Befehl helm install ausführen. Helm bietet mehrere Möglichkeiten, ein Diagramm zu finden und zu installieren, aber am einfachsten ist es, die Bitnami-Diagramme zu verwenden.

        $ helm repo update              # Make sure we get the latest list of charts
        $ helm install bitnami/mysql --generate-name
        NAME: mysql-1612624192
        LAST DEPLOYED: Sat Feb  6 16:09:56 2021
        NAMESPACE: default
        STATUS: deployed
        REVISION: 1
        TEST SUITE: None
        NOTES: ...

![Bild](/attachements/10.png)

Mit Helm ist es einfach zu sehen, was veröffentlicht wurde:

        $ helm list
        NAME            	NAMESPACE	REVISION	UPDATED                             	STATUS  	CHART      	APP VERSION
        mysql-1612624192	default  	1       	2021-02-06 16:09:56.283059 +0100 CET	deployed	mysql-8.3.0	8.0.23

![Bild](/attachements/11.png)

Um eine Version zu deinstallieren, verwenden Sie den Befehl helm uninstall:

        $ helm uninstall mysql-1612624192
        release "mysql-1612624192" uninstalled


        $ helm status mysql-1612624192
        Status: UNINSTALLED
        ...

![Bild](/attachements/12.png)

[QuickstartGuide](https://helm.sh/docs/intro/quickstart/)

## A Starter Chart

Folgende Anleitung wurde durchgemacht: [Getting-Started](https://helm.sh/docs/chart_template_guide/getting_started/)

Für diese Anleitung erstellen wir ein einfaches Diagramm namens mychart und erstellen dann einige Vorlagen innerhalb des Diagramms.

        $ helm create mychart
        Creating mychart



### Ein kurzer Blick auf `mychart/templates/`
Wenn man einen Blick auf das Verzeichnis `mychart/templates/` wirft, wird man feststellen, dass dort bereits einige Dateien vorhanden sind.

- `NOTES.txt` : Der "Hilfetext" für Ihr Diagramm. Dieser wird Ihren Benutzern angezeigt, wenn sie die Helminstallation ausführen.
- `deployment.yam` : Ein grundlegendes Manifest für die Erstellung eines Kubernetes-Einsatzes
- `service.yaml` : Ein grundlegendes Manifest für die Erstellung eines Service-Endpunkts für Ihr Deployment
- `_helpers.tpl` : Ein Ort, an dem Sie Hilfsvorlagen ablegen, die Sie im gesamten Diagramm wiederverwenden können

![Bild](/attachements/13.png)

Diese Dateien werden nun alle gelöscht. Auf diese Weise können wir unser Beispiel von Grund auf aufbauen. Wir werden unsere eigene NOTES.txt und _helpers.tpl erstellen, während wir arbeiten.

Gemäss Anleitung so wird dies so gelöscht:

        $ rm -rf mychart/templates/*

Hat aber bei uns nicht geklappt, wir haben dies so durchgeführt:

        rm -r *

![Bild](/attachements/14.png)

## A First Template

Die erste Vorlage, die wir erstellen werden, ist eine ConfigMap. In Kubernetes ist eine ConfigMap einfach ein Objekt zum Speichern von Konfigurationsdaten. Andere Dinge, wie Pods, können auf die Daten in einer ConfigMap zugreifen.

Da es sich bei ConfigMaps um grundlegende Ressourcen handelt, sind sie ein guter Ausgangspunkt für uns.

Beginnen wir mit der Erstellung einer Datei namens `nano configmap.yaml`:

        apiVersion: v1
        kind: ConfigMap
        metadata:
        name: mychart-configmap
        data:
        myvalue: "Hello World"

![Bild](/attachements/15.png)

![Bild](/attachements/16.png)

Mit dieser einfachen Vorlage haben wir nun ein installierbares chart. Und wir können es wie folgt installieren:

        $ helm install full-coral ./mychart
        NAME: full-coral
        LAST DEPLOYED: Tue Nov  1 17:36:01 2016
        NAMESPACE: default
        STATUS: DEPLOYED
        REVISION: 1
        TEST SUITE: None

![Bild](/attachements/17.png)

Mit Helm können wir die Freigabe abrufen und die tatsächlich geladene Vorlage sehen.

        $ helm get manifest full-coral

        ---
        # Source: mychart/templates/configmap.yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
        name: mychart-configmap
        data:
        myvalue: "Hello World"

![Bild](/attachements/18.png)

Der Befehl helm get manifest nimmt einen Versionsnamen (full-coral) und druckt alle Kubernetes-Ressourcen aus, die auf den Server hochgeladen wurden. Jede Datei beginnt mit ---, um den Beginn eines YAML-Dokuments anzuzeigen, und wird dann von einer automatisch generierten Kommentarzeile gefolgt, die uns sagt, welche Vorlagendatei dieses YAML-Dokument generiert hat.

Von da an können wir sehen, dass die YAML-Daten genau dem entsprechen, was wir in unsere configmap.yaml-Datei geschrieben haben.

Jetzt können wir unsere Version deinstallieren: `helm uninstall full-coral`.

![Bild](/attachements/19.png)

#### [I - Operator Pattern](/aufgaben/I%20-%20Operator%20Pattern.md)
#### [Hauptseite](/README.md)