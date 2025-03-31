# # Documentación: Funcionalidades MongoDB en Aplicación MVC

Este documento describe las funcionalidades implementadas en una aplicación ASP.NET MVC para trabajar con MongoDB alojado en un contenedor Docker. Se incluyen operaciones para crear respaldos, restaurarlos, importar datos desde archivos JSON y exportar colecciones a JSON.

---

## Contenedor Docker Utilizado

El contenedor MongoDB se ejecuta con el siguiente comando:

```bash
docker run -d --name mongoapp \
 -p 27020:27017 \
 -v C:\MongoData:/data \
 mongo:4.0
```

> El volumen `C:\MongoData` está vinculado con `/data` dentro del contenedor para almacenar los respaldos y exportaciones.

---

## 1. Crear Backups de Bases de Datos

**Ruta en la app:** `/Mongo/BackupRestore`

### Campos requeridos:
- Nombre de la base de datos

### Proceso:
1. Se ejecuta `mongodump` dentro del contenedor.
2. Se guarda la copia en `/data/<Base>` que corresponde a `C:\MongoData\<Base>` en el host.

### Comando usado:
```bash
docker exec mongoapp mongodump --db MiBase --out /data/MiBase
```

---

## 2. Restaurar Backups

### Campos requeridos:
- Carpeta (nombre del backup en `/data`, sin ruta)
- Base de datos destino

### Comando usado:
```bash
docker exec mongoapp mongorestore \
 --nsInclude Prueba1.* \
 --nsFrom Prueba1.* \
 --nsTo MiCopiaRestaurada.* \
 /data/Prueba1
```

---

## 3. Importar Datos desde JSON

### Ruta en la app:
`/Mongo/ImportExportMongo`

### Proceso:
1. El usuario selecciona un archivo `.json` desde el host.
2. Se copia al contenedor.
3. Se ejecuta `mongoimport` para insertar los datos.

### Comando usado:
```bash
docker cp archivo.json mongoapp:/tmp/archivo.json

docker exec mongoapp mongoimport \
 --db MiBase \
 --collection MiColeccion \
 --file /tmp/archivo.json \
 --jsonArray
```

---

## 4. Exportar Datos a JSON

### Campos requeridos:
- Base de datos
- Colección

### Proceso:
1. Se ejecuta `mongoexport` dentro del contenedor.
2. Se guarda el archivo directamente en `/data/export.json`, accesible desde `C:\MongoData` en el host.

### Comando usado:
```bash
docker exec mongoapp mongoexport \
 --db MiBase \
 --collection MiColeccion \
 --out /data/export.json \
 --jsonArray
```

---

## Notas Finales

- La interfaz es intuitiva, muestra alertas visuales de éxito/error.
- Todos los datos se gestionan de forma local con respaldo en `C:\MongoData`.
- Se validan errores como archivos no seleccionados, rutas incorrectas o nombres vacíos.

---

### Requisitos
- MongoDB 4.0 en Docker
- ASP.NET MVC + Bootstrap
- Volumen montado entre host y contenedor para sincronización de archivos

---
