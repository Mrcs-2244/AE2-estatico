# AE2: Servidor Web Estático con Zensical y NGINX

## 1. Generación de la web con Zensical
Al estar en una carpeta nueva, utilicé `uvx` para que descargara y ejecutara Zensical. Primero inicialicé la estructura base en la carpeta del proyecto:

`uvx zensical new .`

Una vez creada la estructura, ejecuté el comando de compilación:

`uvx zensical build`

Esto procesó el proyecto y generó una carpeta llamada `site` que contiene todo el HTML y CSS puro que necesita el servidor.

## 2. Configuración en NGINX
Aprovechando que la carpeta está montada en el contenedor, copié los archivos generados al directorio de NGINX y le di permisos de lectura:

`docker exec dpl-lab mkdir -p /var/www/html/zensical`
`docker exec dpl-lab bash -c 'cp -r /home/alumno/dpl/AE2-estatico/zensical-site/site/. /var/www/html/zensical/'`
`docker exec dpl-lab chmod -R a+rX /var/www/html/zensical`

Después, creé un archivo de configuración (`zensical.conf`) donde indico que la ruta raíz (`root`) de la web es esa misma carpeta. Borré la página por defecto de Ubuntu, enlacé mi archivo y reinicié el servicio:

`docker exec dpl-lab rm -f /etc/nginx/sites-enabled/default`
`docker exec dpl-lab ln -s /etc/nginx/sites-available/zensical /etc/nginx/sites-enabled/`
`docker exec dpl-lab nginx -t`
`docker exec dpl-lab nginx -s reload`

## 3. Comprobación del servidor
Para validar que NGINX está sirviendo la página correctamente al exterior por el puerto 80, ejecuté dos pruebas con `curl`:
- `curl -I http://localhost/` -> Comprueba las cabeceras y devuelve un `HTTP/1.1 200 OK`.
- `curl http://localhost/` -> Devuelve todo el código HTML del index de la web. Aquí una muestra de las primeras líneas:

```html
<!doctype html>
<html lang="en" class="no-js">
  <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width,initial-scale=1">
      <title>Get started - Documentation</title>
      <meta name="generator" content="zensical-0.0.68">
      <!-- ... resto del código HTML ... -->
  </head>
  <body>
      <!-- Contenido de la página -->
  </body>
</html>
