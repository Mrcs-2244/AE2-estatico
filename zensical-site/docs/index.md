# Despliegue de la Documentación con Zensical y NGINX

Para la primera parte de la práctica, monté la documentación estática con Zensical. Inicialicé la estructura base con `uvx` para no tener que instalar dependencias globales:

```bash
uvx zensical new .
```

Una vez editados los archivos de la documentación, lancé la compilación del proyecto:

```bash
uvx zensical build
```

Esto procesó el proyecto y generó una carpeta (normalmente `site` o `build`) con todo el HTML y CSS estático.

### Integración con NGINX

Aprovechando que el contenedor de NGINX ya estaba corriendo, creé la carpeta destino en el servidor, le pasé los archivos generados y me aseguré de darle permisos de lectura para evitar errores 403 (Forbidden):

```bash
docker exec dpl-lab-web mkdir -p /var/www/html/zensical
docker cp zensical-site/build/. dpl-lab-web:/var/www/html/zensical/
docker exec dpl-lab-web chmod -R a+rX /var/www/html/zensical
```

Para que NGINX supiera qué hacer con esto, creé un archivo de configuración (`zensical.conf`) apuntando el `root` a `/var/www/html/zensical`. Después, borré la página por defecto de Ubuntu, creé el enlace simbólico para activar mi sitio y verifiqué la sintaxis antes de reiniciar el servicio:

```bash
docker exec dpl-lab-web rm -f /etc/nginx/sites-enabled/default
docker exec dpl-lab-web ln -s /etc/nginx/sites-available/zensical /etc/nginx/sites-enabled/
docker exec dpl-lab-web nginx -t
docker exec dpl-lab-web nginx -s reload
```

### Comprobación del servidor

Para validar que NGINX está sirviendo la página correctamente al exterior, ejecuté dos pruebas rápidas con `curl` (apuntando al puerto 8080 que es el que tengo mapeado al host):

1. **Test de cabeceras:**
   ```bash
   curl -I http://localhost:8080/
   ```
   *Resultado:* Me devolvió un `HTTP/1.1 200 OK`, confirmando que el servidor responde sin errores.

2. **Test de contenido:**
   ```bash
   curl http://localhost:8080/
   ```
   *Resultado:* Devolvió todo el código HTML de la portada de Zensical por la terminal, confirmando que la ruta del `root` es correcta.