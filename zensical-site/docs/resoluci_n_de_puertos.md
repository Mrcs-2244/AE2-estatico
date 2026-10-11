# Resolución de conflictos: Mapeo al puerto 8080

Durante la fase de configuración, antes de levantar el servicio, realicé una copia de seguridad del entorno de trabajo original (```cp -r dpl-lab dpl-lab-terminado```) para preservar los archivos de configuración base antes de iniciar el despliegue final.

Al intentar desplegar la web Zensical en el nuevo entorno, me encontré con un conflicto de puertos. El contenedor NGINX original no lograba iniciarse debido a que el puerto 80 del host (Ubuntu) ya se encontraba en uso por otro proceso del sistema. Tras intentar detener los procesos conflictivos en el host sin éxito, opté por la solución más estandarizada en contenedores: modificar el mapeo de puertos.

Para resolverlo, levanté un nuevo contenedor bajo el nombre ```dpl-lab-web```, redirigiendo el tráfico del puerto 8080 de mi máquina física hacia el puerto 80 del contenedor, permitiendo así el acceso a los sitios mediante los nombres de dominio locales. El comando ejecutado fue el siguiente:
```bash
docker run -dit --name dpl-lab-web -p 8080:80 dpl-lab-terminado /bin/bash
```
