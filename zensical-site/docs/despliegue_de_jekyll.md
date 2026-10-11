# Despliegue del Blog con Jekyll (Virtual Host)

Para montar el segundo sitio web en el mismo servidor (Virtual Host basado en nombres), decidí usar la plantilla oficial de Jekyll (Minima) para evitar problemas de dependencias con gemas de Ruby. 

Creé el proyecto desde cero:
```bash
jekyll new jekyll
```

Antes de compilar, entré al archivo `_config.yml` y ajusté la variable de la URL para que los estilos (CSS) supieran dónde buscarse dentro de mi entorno local:
`url: "http://blog.localhost:8080"`

Después, generé los estáticos:
```bash
cd jekyll
jekyll build
```

Esto genera el código final en la carpeta oculta `_site`. Igual que hice con Zensical, copié estos archivos a su propia carpeta en el contenedor:

```bash
docker cp _site/. dpl-lab-web:/var/www/html/jekyll/
```

### Configuración del Virtual Host

Para separar esta web de la de Zensical y servir ambas simultáneamente, creé el archivo `jekyll.conf` en `sites-available`. Aquí la clave fue indicar `server_name blog.localhost;` y apuntar el `root` a `/var/www/html/jekyll;`. 

Lo activé con su enlace simbólico en `sites-enabled` y recargué NGINX (`nginx -s reload`).

### Comprobación del enrutamiento

Para comprobar que el Virtual Host funciona y sabe qué web mostrar según el dominio que se pida, usé `curl` inyectando manualmente la cabecera `Host`:

```bash
curl -H "Host: blog.localhost" http://localhost:8080/
```

Al hacer esto, NGINX intercepta el nombre del dominio y devuelve correctamente el HTML del blog de Jekyll, demostrando que el Virtual Host por nombres está perfectamente configurado.