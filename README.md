// ...existing code...
# Instalación local de MongoDB (sin Docker)

Resumen
-------
Guía rápida para instalar y verificar MongoDB Community Server localmente y ejemplos básicos de uso. Está enfocada en Ubuntu (devcontainer), con notas para Windows y macOS.

Nota rápida para este contenedor
-------------------------------
Para abrir un enlace en el navegador del host desde aquí:
$BROWSER https://www.mongodb.com/try/download/community

Índice
------
- TL;DR (Ubuntu)
- Prerrequisitos
- Instalación en Ubuntu (ejemplo)
- Instalación en Windows (resumen)
- Verificación y comandos útiles
- Quickstart: CRUD en `myapp`
- Validación de esquema e índices
- Seguridad básica
- Problemas comunes
- Recursos

TL;DR (Ubuntu)
---------------
```bash
sudo apt update
sudo apt install -y gnupg wget lsb-release
# Añade la llave GPG y el repo (usa lsb_release -sc para la distro)
wget -qO - https://www.mongodb.org/static/pgp/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-server-7.gpg
echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-7.gpg ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -sc)/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update
sudo apt install -y mongodb-org
sudo systemctl enable --now mongod
# Verificar:
mongod --version
mongosh --version
```

Prerrequisitos
--------------
- Acceso con permisos sudo para instalar paquetes y manejar systemd.
- Conexión a Internet.
- En este contenedor: `$BROWSER <url>` para abrir links en el host.

Instalación en Windows (resumen)
-------------------------------
- Descargar: https://www.mongodb.com/try/download/community
- O con gestores:
  - Chocolatey: `choco install mongodb`
  - winget:
    - `winget install -e --id MongoDB.Server`
    - `winget install -e --id MongoDB.Shell`
    - `winget install -e --id MongoDB.Compass`
- Iniciar servicio: `net start MongoDB`
- Añadir `C:\Program Files\MongoDB\Server\<versión>\bin` al PATH si hace falta.

Verificación y comandos útiles
------------------------------
- `mongod --version` — versión del servidor.
- `mongosh --version` — shell cliente moderno.
- Logs (Linux): `/var/log/mongodb/mongod.log`
- Data dir por defecto: `/var/lib/mongodb` (configurable en `/etc/mongod.conf`)
- Ver estado del servicio: `sudo systemctl status mongod`

Quickstart: CRUD en la base `myapp`
----------------------------------
Conéctate con `mongosh` y ejecuta:

1) Crear base y colección
```js
use myapp
db.createCollection("productos")
```

2) Insertar documentos
```js
db.productos.insertMany([
  { nombre: "Mouse Gamer", precio: 2500, stock: 15 },
  { nombre: "Teclado Mecánico", precio: 4500, stock: 10 }
])
```

3) Consultar
```js
db.productos.find({ precio: { $gt: 3000 } })
```

4) Actualizar
```js
db.productos.updateOne(
  { nombre: "Mouse Gamer" },
  { $set: { stock: 20 } }
)
```

Relaciones y ejemplos rápidos
-----------------------------
- Referencias por ObjectId entre colecciones (users, orders, products, reviews).
```js
db.users.insertOne({ nombre: "Tomás", email: "tomas@example.com" })
db.orders.insertOne({
  usuario_id: ObjectId("..."),
  productos: [{ producto_id: ObjectId("..."), cantidad: 2 }],
  estado: "pendiente"
})
```

Validación de esquema (opcional)
--------------------------------
```js
db.createCollection("orders", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["usuario_id", "productos", "estado"],
      properties: {
        estado: { enum: ["pendiente", "enviado", "cancelado"] },
        productos: {
          bsonType: "array",
          items: {
            bsonType: "object",
            required: ["producto_id", "cantidad"]
          }
        }
      }
    }
  }
})
```

Índices recomendados
--------------------
```js
db.products.createIndex({ nombre: 1 })
db.orders.createIndex({ usuario_id: 1 })
db.reviews.createIndex({ producto_id: 1 })
```

Seguridad básica
----------------
1. Crear usuario administrador:
```js
use admin
db.createUser({
  user: "admin",
  pwd: "tu_password_segura",
  roles: [{ role: "root", db: "admin" }]
})
```
2. Habilitar autenticación en `/etc/mongod.conf`:
security:
  authorization: "enabled"
y reiniciar `sudo systemctl restart mongod`.
3. Revisar `bindIp` en la configuración para limitar accesos.

Problemas comunes
-----------------
- mongod no arranca: revisar `/var/log/mongodb/mongod.log`.
- Puerto 27017 en uso: `ss -ltnp | grep 27017`.
- Permisos en data dir: `sudo chown -R mongodb:mongodb /var/lib/mongodb`.

Recursos
--------
- Descargas: https://www.mongodb.com/try/download/community  (abrir con `$BROWSER <url>`)
- Documentación: https://www.mongodb.com/docs/
- MongoDB University: cursos gratuitos.

Licencia
--------
Añadí un archivo LICENSE si querés publicar; por defecto este README no impone licencia.

Si querés, escribo este contenido directamente en /workspaces/intalacion_mongodb_practica/README.md ahora. ¿Lo aplico? 
```// filepath: /workspaces/intalacion_mongodb_practica/README.md
// ...existing code...
# Instalación local de MongoDB (sin Docker)

Resumen
-------
Guía rápida para instalar y verificar MongoDB Community Server localmente y ejemplos básicos de uso. Está enfocada en Ubuntu (devcontainer), con notas para Windows y macOS.

Nota rápida para este contenedor
-------------------------------
Para abrir un enlace en el navegador del host desde aquí:
$BROWSER https://www.mongodb.com/try/download/community

Índice
------
- TL;DR (Ubuntu)
- Prerrequisitos
- Instalación en Ubuntu (ejemplo)
- Instalación en Windows (resumen)
- Verificación y comandos útiles
- Quickstart: CRUD en `myapp`
- Validación de esquema e índices
- Seguridad básica
- Problemas comunes
- Recursos

TL;DR (Ubuntu)
---------------
```bash
sudo apt update
sudo apt install -y gnupg wget lsb-release
# Añade la llave GPG y el repo (usa lsb_release -sc para la distro)
wget -qO - https://www.mongodb.org/static/pgp/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-server-7.gpg
echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-7.gpg ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -sc)/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update
sudo apt install -y mongodb-org
sudo systemctl enable --now mongod
# Verificar:
mongod --version
mongosh --version
```

Prerrequisitos
--------------
- Acceso con permisos sudo para instalar paquetes y manejar systemd.
- Conexión a Internet.
- En este contenedor: `$BROWSER <url>` para abrir links en el host.

Instalación en Windows (resumen)
-------------------------------
- Descargar: https://www.mongodb.com/try/download/community
- O con gestores:
  - Chocolatey: `choco install mongodb`
  - winget:
    - `winget install -e --id MongoDB.Server`
    - `winget install -e --id MongoDB.Shell`
    - `winget install -e --id MongoDB.Compass`
- Iniciar servicio: `net start MongoDB`
- Añadir `C:\Program Files\MongoDB\Server\<versión>\bin` al PATH si hace falta.

Verificación y comandos útiles
------------------------------
- `mongod --version` — versión del servidor.
- `mongosh --version` — shell cliente moderno.
- Logs (Linux): `/var/log/mongodb/mongod.log`
- Data dir por defecto: `/var/lib/mongodb` (configurable en `/etc/mongod.conf`)
- Ver estado del servicio: `sudo systemctl status mongod`

Quickstart: CRUD en la base `myapp`
----------------------------------
Conéctate con `mongosh` y ejecuta:

1) Crear base y colección
```js
use myapp
db.createCollection("productos")
```

2) Insertar documentos
```js
db.productos.insertMany([
  { nombre: "Mouse Gamer", precio: 2500, stock: 15 },
  { nombre: "Teclado Mecánico", precio: 4500, stock: 10 }
])
```

3) Consultar
```js
db.productos.find({ precio: { $gt: 3000 } })
```

4) Actualizar
```js
db.productos.updateOne(
  { nombre: "Mouse Gamer" },
  { $set: { stock: 20 } }
)
```

Relaciones y ejemplos rápidos
-----------------------------
- Referencias por ObjectId entre colecciones (users, orders, products, reviews).
```js
db.users.insertOne({ nombre: "Tomás", email: "tomas@example.com" })
db.orders.insertOne({
  usuario_id: ObjectId("..."),
  productos: [{ producto_id: ObjectId("..."), cantidad: 2 }],
  estado: "pendiente"
})
```

Validación de esquema (opcional)
--------------------------------
```js
db.createCollection("orders", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["usuario_id", "productos", "estado"],
      properties: {
        estado: { enum: ["pendiente", "enviado", "cancelado"] },
        productos: {
          bsonType: "array",
          items: {
            bsonType: "object",
            required: ["producto_id", "cantidad"]
          }
        }
      }
    }
  }
})
```

Índices recomendados
--------------------
```js
db.products.createIndex({ nombre: 1 })
db.orders.createIndex({ usuario_id: 1 })
db.reviews.createIndex({ producto_id: 1 })
```

Seguridad básica
----------------
1. Crear usuario administrador:
```js
use admin
db.createUser({
  user: "admin",
  pwd: "tu_password_segura",
  roles: [{ role: "root", db: "admin" }]
})
```
2. Habilitar autenticación en `/etc/mongod.conf`:
security:
  authorization: "enabled"
y reiniciar `sudo systemctl restart mongod`.
3. Revisar `bindIp` en la configuración para limitar accesos.

Problemas comunes
-----------------
- mongod no arranca: revisar `/var/log/mongodb/mongod.log`.
- Puerto 27017 en uso: `ss -ltnp | grep 27017`.
- Permisos en data dir: `sudo chown -R mongodb:mongodb /var/lib/mongodb`.

Recursos
--------
- Descargas: https://www.mongodb.com/try/download/community  (abrir con `$BROWSER <url>`)
- Documentación: https://www.mongodb.com/docs/
- MongoDB University: cursos gratuitos.

