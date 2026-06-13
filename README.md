# Tercera evaluación de DevOps
Esta evaluación tiene como objetivo que el estudiante desmuestre la **capacidad de integrar herramientas de monitoreo, dashboards y políticas de cumplimiento automatizado dentro de un pipeline CI/CD previamente configurado para su microservicio.** El trabajo deber considerar la trazabilidad completa desde el desarrollo hasta la producción simulada.

## Herramientas a utilizar
* **Git**🌿
* **Docker**🐋
* **Docker compose**🐳⚙️
* **Kubernetes** ☸️
* **Grafena**📊
* **Prometheus**🔥

## A: Contenerización Local
Antes de desplegar en produccion, el código debe convertirse en un artefacto inmutable (una imagen Docker).
```bash
# 1. Compilar el proyecto de Spring Boot (Genera el archivo ejecutable .jar)
# Explicación: Limpia compilaciones viejas y empaqueta la app saltando los tests temporales de BD.
mvn clean package -DskipTests

# 2. Construir la imagen Docker de la aplicación (Paso 3)
# Explicación: Lee el Dockerfile de la raíz y empaqueta el entorno (Java 17 + el .jar) bajo el nombre 'springboot-app'.
docker build -t springboot-app:latest .

# 3. Levantar el entorno de desarrollo multi-contenedor (Paso 6)
# Explicación: Levanta en segundo plano (-d) la API de Java y la base de datos MySQL vinculadas en la misma red local.
docker-compose up -d --build
```
## B: Orquestación con Kubernetes
1. Aplicar la infraestructura declarativa en el clúster (Paso 7 y 8)
Explicación: Envía todos los archivos YAML de la carpeta k8s/ al motor de Kubernetes para crear los Deployments y Services.
```bash
kubectl apply -f k8s/
```

3. Monitorear el estado de los componentes en tiempo real (Paso 11)
Explicación: Muestra el ciclo de vida de los pods. Deben cambiar de 'ContainerCreating' a 'Running'.
```bash
kubectl get pods -w
```

5. Auditoría de Logs en Vivo (Paso 12)
Explicación: Conecta la terminal directamente a la salida estándar del pod de Spring Boot para ver errores, consultas SQL o arranques en tiempo real.
```bash
kubectl logs deployment/springboot-deployment --tail=100 -f
```

## C: Telemetría y Acceso a Monitoreo
1. Redirección de la API REST de Spring Boot
Explicación: Mapea el puerto local 8080 hacia el puerto interno 8080 del pod.
```bash
kubectl port-forward deployment/springboot-deployment 8080:8080.
```
👉 URL de acceso en el navegador: http://localhost:8080/usuarios

3. Redirección del Servidor de Prometheus (Paso 9)
Explicación: Permite acceder a la consola de Prometheus para verificar que los "Targets" (orígenes de datos) estén saludables.
```bash
kubectl port-forward deployment/prometheus-deployment 9090:9090
```
👉 URL de acceso en el navegador: http://localhost:9090

5. Redirección del Panel Visual de Grafana (Paso 10 y 11)
Explicación: Abre la interfaz de usuario de Grafana para interactuar con los Dashboards construidos.
```bash
kubectl port-forward deployment/grafana-deployment 3000:3000
```
👉 URL de acceso en el navegador: http://localhost:3000 (Credenciales: admin / admin)
