# Como instalar Odoo en Render

En esta guia veremos como instalar el CRM Odoo en Render.

## 1. Creacion de proyecto
Primero de todo, debemos crear los ficheros requeridos para que nuestro futuro servidor funcione correctamente, para ello crearemos la siguente estructura de ficheros:

- extra_addons
	- dummy_module
		- _____init_____.py
		- _____manifest_____.py
	- .gitkeep
- Dockerfile

## 2. Rellenar fichero Dockerfile y manifest.py
En el interior de nuestro fichero Dockerfile, introduciremos lo siguiente:
```
# Imagen base Odoo 17
FROM odoo:17
# (Opcional) módulos propios
COPY ./extra-addons /mnt/extra-addons
# Puerto HTTP de Odoo
EXPOSE 8069
# Puerto por defecto de PostgreSQL
ENV PGPORT=5432
# 1) Inicializa la BD indicada en $PGDATABASE si está vacía (stop-after-init)
# 2) Después arranca el servidor normalmente
#
# NOTA: usamos $PGDATABASE para que la inicialización vaya contra esa BD
# y --db-filter la fije para evitar que Odoo “coja” otra por error.
CMD ["bash","-lc", "\
echo '==> Checking/initializing DB $PGDATABASE' && \
odoo -d $PGDATABASE -i base --without-demo=all \
--db_host=$PGHOST --db_port=$PGPORT \
--db_user=$PGUSER --db_password=$PGPASSWORD \
--addons-path=/usr/lib/python3/dist-packages/odoo/addons,/mnt/extra-addons \
--stop-after-init || true; \
echo '==> Starting Odoo server' && \
odoo --db_host=$PGHOST --db_port=$PGPORT \
--db_user=$PGUSER --db_password=$PGPASSWORD \
--addons-path=/usr/lib/python3/dist-packages/odoo/addons,/mnt/extra-addons \
--db-filter=$PGDATABASE \
--dev=all"]
```

Y a continuacion, en el fichero __manifest__.py introduciremos lo siguiente:

```
{
    "name": "Dummy Module",
    "version": "1.0",
    "summary": "Módulo vacío de prueba",
    "installable": True,
}

```


## 3. Registrarse en Render
Una vez tenemos nuestro repo de GitHub listo, nos dirigimos a [Render](https://render.com) y nos registramos con nuestra cuenta de GitHub.

## 4. Crear web service
Cuando estemos en nuestro dashboard de Render, procedemos a crear un servidor que pueda alojar nuestro contenedor de Docker con Odoo, para ello creamos un nuevo 'Web Service' y en el selector 'Git Provider' donde veremos todos nuestros repos, seleccionamos el nombre del repo que acabamos de crear.

__NOTA__: Antes de crearlo, debemos asegurarnos de que:

- El lenguaje esta configurado a 'Docker'
- El tipo de instancia (Instance type) esta seleccionado el __'Free'__

Una vez nos aseguremos de esto, podemos proceder con la creacion del web service.

## 5. Crear base de datos Postgres
Con el web service creado, podemos proceder a crear la base de datos postgre que alojara nuestros datos de Odoo.
Para ello, en el dashboard de Render, creamos un nuevo __Postgres__ asegurandonos de nuevo, de que el 'Instance type' es __Free__

## 6. Copiar datos de conexion
Una vez tenemos nuestra base de datos Postgres, debemos copiar los datos de acceso de nuestra base de datos para indicarle a nuestro web server como acceder, para ello debemos entrar en la base de datos en la web de render y bajar hasta el apartado 'Connections' donde veremos:

- Hostname
- Port
- Database
- Password

Estos parametros deberemos copiarlos para introducirlos en el siguiente paso como variables de entorno en nuestro web service.

## 7. Configurar variables de entorno en Web Service
A continuacion, debemos entrar en nuestro web service y en la barra lateral seleccionaremos 'Environment' y ahi, introduciremos la clave y el valor de los datos.

__IMPORTANTE:__ La clave de estos datos, debemos copiarla de nuestro Dockerfile donde encontraremos valores como __'$PGHOST'__ por ejemplo, este sera la clave de la variable del host, esto se aplica para el resto de parametros, cada uno con su clave.

El valor, lo debemos haber copiado en el paso anterior, asi que simplemente lo pegamos cada uno con su clave y ya tendriamos nuestro contenedor de Docker con Odoo listo para funcionar.
