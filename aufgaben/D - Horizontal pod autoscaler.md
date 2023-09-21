[2_Unterrichtsressourcen/D · main · TBZ-HF-Informatik / modules for students / Cloud Native Technology Vertiefung · GitLab](https://gitlab.com/ch-tbz-hf/Stud/v-cnt/-/tree/main/2_Unterrichtsressourcen/D)
#### [Hauptseite](/README.md)
#### [C - Init container](/aufgaben/C%20-%20Init%20container.md)
# D - Horizontal pod autoscaler

## Deploy a sample application

Run and expose php-apache server

        $ cat php-apache.yml

        $ kubectl apply -f php-apache.yaml

Ausgabe:

        deployment.apps/php-apache created
        service/php-apache created

## Create the Kubernetes service

        kubectl version


        kubectl get pods --namespace kube-system


        kubectl create deployment php-apache --image=php:7.4-apache -n kube-system


        kubectl autoscale deployment php-apache --namespace kube-system --cpu-percent=50 --min=1 --max=10

Ausgabe:

        horizontalpodautoscaler.autoscaling/php-apache autoscaled


        kubectl get pods --namespace kube-system --show-kind=true


        kubectl get hpa  --namespace kube-system --show-kind=true

Increade the load:

         kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"


         kubectl get hpa php-apache --watch



test
#### [E - Zugriffsteuerung](/aufgaben/E%20-%20Zugriffsteuerung.md)
#### [Hauptseite](/README.md)