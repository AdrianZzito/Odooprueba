🧩 Instalación paso a paso de Odoo 17 en Render (desde cero)



Este documento está pensado para personas que nunca instalaron Odoo ni usaron Render.
Explica, de forma clara y completa, cómo pasar de cero a tener Odoo 17 funcionando online usando GitHub y Render.

🚀 1. Qué es Odoo y qué haremos



Odoo es un sistema ERP (Enterprise Resource Planning) de código abierto: una aplicación web que sirve para gestionar empresas (ventas, compras, inventario, facturación, etc.).




En este tutorial aprenderás a:

Crear un repositorio GitHub con los archivos necesarios.
Subir tu proyecto Odoo (con un módulo personalizado).
Conectarlo con una base de datos PostgreSQL.
Desplegarlo en Render, una plataforma que aloja aplicaciones web fácilmente.
Ver Odoo funcionando en Internet.
🧰 2. Qué necesitas antes de empezar
Una cuenta en GitHub.
Una cuenta en Render.
Conexión a Internet y navegador web.

🧱 3. Crear el repositorio en GitHub
Entra en tu cuenta de GitHub.
Haz clic en New Repository.
Nómbralo por ejemplo: odoo_render_demo.
Marca la casilla Add a README file.
Haz clic en Create repository.



Cuando el repositorio esté listo, tendrás una URL parecida a:

https://github.com/tu-usuario/odoo_render_demo

📂 4. Archivos que debe tener tu repositorio



Crea la siguiente estructura dentro de tu repositorio:

odoo_render_demo/
├── Dockerfile
├── README.md
└── extra-addons/
    └── dummy_module/
        ├── __init__.py
        ├── __manifest__.py
        └── .gitkeep






⚙️ 5. Crear el archivo Dockerfile



Este archivo le dice a Render cómo construir la aplicación Odoo.




Codigo Dockerfile:

# Imagen base Odoo 17 (oficial)
FROM odoo:17

# Copiamos los módulos personalizados
COPY ./extra-addons /mnt/extra-addons

# Puerto donde Odoo escucha
EXPOSE 8069

# Puerto por defecto de PostgreSQL
ENV PGPORT=5432

# Inicia la base de datos (si no existe) y arranca el servidor Odoo
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




Este archivo crea una imagen de Odoo 17 lista para conectarse a una base de datos PostgreSQL.

☁️ 6. Crear los servicios en Render
🔹 Paso 1: Base de datos PostgreSQL
Entra en Render.
Haz clic en New → Database.
Elige PostgreSQL y ponle nombre, por ejemplo odoo-db.
Espera unos segundos a que se cree.



Render te mostrará información como esta:

Dato	Ejemplo
Hostname	dpg-d3ts1m2li9vc73bl59j0-a
Port	5432
Database	postgres_db_oxie
User	user
Password	ThjGytfyAc32XtVCcTe6eU6QMpQymoIW



Guarda estos datos, los usaremos en el siguiente paso.

🔹 Paso 2: Servicio web (Odoo)
En Render, haz clic en New → Web Service.
Conecta tu cuenta GitHub y selecciona el repositorio odoo_render_demo.
Configura:
Branch: main
Runtime: Docker
Dockerfile Path: ./Dockerfile
Auto Deploy: Activado ✅
Añade las siguientes variables de entorno (Render → Environment → Add Environment Variable):
Variable	Valor
PGHOST	dpg-d3ts1m2li9vc73bl59j0-a
PGPORT	5432
PGUSER	user
PGPASSWORD	ThjGytfyAc32XtVCcTe6eU6QMpQymoIW
PGDATABASE	postgres_db_oxie



Guarda los cambios y Render comenzará a construir tu aplicación Odoo automáticamente.
Cuando termine, verás el estado ✅ Deployed.

🌍 7. Ver Odoo funcionando



Render te mostrará una URL como esta:




🔗 https://odoo-test-7ruu.onrender.com




Abre esa dirección en tu navegador.




Si todo salió bien, aparecerá la pantalla de instalación de Odoo.
Ahí podrás crear tu primera base de datos y entrar en la interfaz web.




Luego, para comprobar que el módulo funciona:

En Odoo, ve a Aplicaciones.
Haz clic en Actualizar lista de aplicaciones.
Busca “Dummy Module”.
Instálalo.



🎉 ¡Listo! Tu Odoo está en línea y tu módulo se carga correctamente.

Autor: Marcos Mateos Iglesias
Ayuda de ChatGPT para el decoramiento de emojis del Readme, TODAS LAS IDEAS FUERON PROPIAS DEL AUTOR. Gracias por leer :D .

