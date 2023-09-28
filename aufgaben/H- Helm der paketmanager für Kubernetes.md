[2_Unterrichtsressourcen/H · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/H)
#### [Hauptseite](/README.md)
#### [E - Zugriffsteuerung](/aufgaben/E%20-%20Zugriffsteuerung.md)
# H - Helm der paketmanager für Kubernetes

## Installation



Helm verfügt jetzt über ein Installationsskript, das automatisch die neueste Version von Helm abruft und lokal installiert.

Sie können dieses Skript abrufen und es dann lokal ausführen. Es ist gut dokumentiert, so dass Sie es durchlesen und verstehen können, was es tut, bevor Sie es ausführen.

        $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
        $ chmod 700 get_helm.sh
        $ ./get_helm.sh

[Installationsanleitung](https://helm.sh/docs/intro/install/)


## Initialize a Helm Chart Repository

Sobald man Helm bereit hat, kann man ein Diagramm-Repository hinzufügen.

        $ helm repo add bitnami https://charts.bitnami.com/bitnami

Sobald diese installiert ist, kann man die charts auflisten, die man installieren kann:

        $ helm search repo bitnami

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

Mit Helm ist es einfach zu sehen, was veröffentlicht wurde:

        $ helm list
        NAME            	NAMESPACE	REVISION	UPDATED                             	STATUS  	CHART      	APP VERSION
        mysql-1612624192	default  	1       	2021-02-06 16:09:56.283059 +0100 CET	deployed	mysql-8.3.0	8.0.23

Uninstall a Release
Um eine Version zu deinstallieren, verwenden Sie den Befehl helm uninstall:

        $ helm uninstall mysql-1612624192
        release "mysql-1612624192" uninstalled


        $ helm status mysql-1612624192
        Status: UNINSTALLED
        ...


#### [I - Operator Pattern](/aufgaben/I%20-%20Operator%20Pattern.md)
#### [Hauptseite](/README.md)