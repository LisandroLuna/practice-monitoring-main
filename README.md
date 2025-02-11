## Requisitos

### Helm: 
Instalacion: https://helm.sh/docs/intro/install/#:~:text=curl%20%2DfsSL%20%2Do,./get_helm.sh

Helm es un gestor de paquetes para Kubernetes que simplifica la instalación, actualización y gestión de aplicaciones en clústeres de Kubernetes. Los Helm Charts son paquetes de Helm que contienen todas las definiciones de recursos de Kubernetes necesarias para desplegar una aplicación. Piensa en ellos como "aplicaciones para Kubernetes" en forma de código, donde cada chart puede incluir:

* Templates: Archivos YAML o JSON que definen los recursos de Kubernetes.
* Values: Un archivo que permite la configuración de las plantillas mediante variables.
* Dependencies: Charts adicionales que pueden ser requeridos por el chart principal.

Los charts permiten a los usuarios empaquetar, versionar, compartir y desplegar aplicaciones de manera reproducible y eficiente, facilitando así la gestión de aplicaciones complejas en entornos Kubernetes.

### Lens (Opcional, es una alternativa grafica a kubectl)
Instalacion: https://k8slens.dev/

Video explicativo: https://www.youtube.com/watch?v=DFMKcR4BqwM

Pide registro pero es gratuita.

Lens es una IDE (Entorno de Desarrollo Integrado) diseñada específicamente para Kubernetes, que simplifica la administración, el monitoreo y la depuración de aplicaciones en clústeres de Kubernetes. Lens ofrece:

* Interfaz de Usuario Intuitiva: Facilita la navegación y gestión de múltiples clústeres de Kubernetes sin necesidad de usar comandos de línea como kubectl.
* Visualización en Tiempo Real: Permite ver métricas, eventos, y registros en tiempo real, mejorando la capacidad de respuesta y troubleshooting.
* Gestión de Recursos: Permite explorar, editar y gestionar todos los recursos de Kubernetes desde una única interfaz.
* Integración con Helm: Aunque Lens no es un gestor de paquetes, soporta la interacción con Helm charts, permitiendo la instalación, actualización y gestión de aplicaciones empacadas en Helm Charts directamente desde su interfaz.

Lens es una herramienta poderosa para desarrolladores y operadores de Kubernetes, ofreciendo una visión clara y un control centralizado de los entornos de Kubernetes, lo que mejora significativamente la productividad y la eficacia en la gestión de aplicaciones.

## Stack de monitoreo de Telegraf + InfluxDB + Grafana

### Configuración Inicial

- `kubectl create ns monitoring`
  - **Descripción**: Este comando crea un nuevo namespace en Kubernetes llamado `monitoring`. 
  Los namespaces proporcionan una forma de dividir recursos de clúster entre múltiples usuarios.

### Configuración de Repositorios Helm

- `helm repo add influxdata https://helm.influxdata.com`
  - **Descripción**: Añade el repositorio de InfluxData a Helm, lo que permite instalar gráficos de InfluxData.

- `helm repo add grafana https://grafana.github.io/helm-charts`
  - **Descripción**: Añade el repositorio de Grafana a Helm, facilitando la instalación de gráficos específicos de Grafana.

### Arquitectura del stack de monitoreo de Telegraf + InfluxDB + Grafana
- Aqui se incluyen dos nodos con Telegraf pero en nuestro caso utilizamos solo uno:
![Diagrama](https://user-images.githubusercontent.com/7296281/190256548-370ceccd-b5a2-47e2-86ef-de0c3b3fe299.png)

### Gestión de Dependencias y Despliegue del Stack de Monitoreo

- `helm dependency build ./monitoring-stack`
  - **Descripción**: Actualiza y descarga las dependencias definidas en el archivo `Chart.yaml` del directorio `monitoring-stack`, preparando el gráfico para la instalación.

- `helm install monitoring-stack ./monitoring-stack --namespace monitoring`
  - **Descripción**: Instala el stack de monitoreo definido en `monitoring-stack` en el namespace `monitoring`. Este comando inicia la creación de los recursos definidos en el gráfico Helm.

- `helm upgrade --install monitoring-stack ./monitoring-stack --namespace monitoring`
  - **Descripción**: Actualiza o instala el stack de monitoreo. Si el stack ya existe, se actualiza; si no, se instala.

### Acceso a Grafana (En caso de no utilizar ingress)

- `kubectl port-forward -n monitoring svc/monitoring-stack-grafana 3000:80`
  - **Descripción**: Redirige el puerto 80 del servicio de Grafana dentro del namespace `monitoring` al puerto 3000 de tu máquina local, permitiendo el acceso a la interfaz de Grafana a través de `localhost:3000`.

- **DASHBOARD ID 928**
  - **Descripción**: Identificador del dashboard de Grafana que se importará para visualizar datos específicos desde el sitio de Grafana.

## Instalacion de componentes individualmente

```
kubectl create ns monitoring

helm repo add influxdata https://helm.influxdata.com

helm update

helm upgrade -i telegraf influxdata/telegraf -n monitoring --version 1.8.54 -f telegraf.yaml 

helm upgrade -i influxdb influxdata/influxdb -n monitoring --version 4.12.5 -f influxdb.yaml 

helm repo add grafana https://grafana.github.io/helm-charts
helm upgrade -i grafana grafana/grafana -n monitoring --version 8.5.1 -f grafana.yaml 

```


## Despliegue de Stack Prometheus

### Configuración de Prometheus

- `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`
  - **Descripción**: Añade el repositorio de Helm de la comunidad de Prometheus, permitiendo la instalación de gráficos relacionados con Prometheus.

- `helm repo update`
  - **Descripción**: Actualiza la lista de gráficos disponibles desde los repositorios de Helm configurados.

- `helm upgrade --install prometheus -n monitoring prometheus-community/kube-prometheus-stack -f prometheus.yaml --version 62.7.0`
  - **Descripción**: Instala o actualiza Prometheus usando el gráfico `kube-prometheus-stack` con una configuración personalizada en `prometheus.yaml` y especificando una versión específica del gráfico.

- `kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090` (Si no utilizamos ingress)
  - **Descripción**: Redirige el puerto 9090 del servicio de Prometheus al mismo puerto en tu máquina local para acceder a la interfaz de Prometheus.

- **URL para datasource: http://prometheus-kube-prometheus-prometheus.monitoring**
  - **Descripción**: URL que se usa para configurar Prometheus como una fuente de datos en Grafana.

- **DASHBOARD ID 1860**
  - **Descripción**: Identificador de dashboard para Grafana, relacionado con el monitoreo de Kubernetes usando Prometheus.

## Despliegue de Loki

### Configuración de Loki

- `helm upgrade --install loki-stack -n monitoring grafana/loki-stack --values loki.yaml`
  - **Descripción**: Instala o actualiza Loki, un sistema de registro para Kubernetes, usando valores personalizados de `loki.yaml` en el namespace `monitoring`.