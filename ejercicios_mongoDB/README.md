Autor: Ing. Martin Acosta - 2020

# Ejercitación MongoDB
## Arquitectura de Datos - CEIoT - FIUBA
### Abrir una terminal en el directorio donde haya guardado el archivo “facturas.json” y usar el siguiente comando para importar la colección de facturas a la db finanzas
```sh
mongoimport -d finanzas -c facturas --file facturas.json
```
### Abrir el shell de MongoDB (mongo) y pararse en la db finanzas
```sh
mongo
use finanzas
```
### Realizar las siguientes operaciones
* Consultar la cantidad de documentos insertados.
```sh
db.facturas.count()
```
```sh
100
```
* Obtener 1 sólo documento para ver el esquema y los nombres de los campos. Sin
mostrar el _id.
```sh
db.facturas.findOne({},{_id:0})
```
```sh
{
        "cliente" : {
                "apellido" : "Malinez",
                "cuit" : 2740488484,
                "nombre" : "Marina",
                "region" : "CENTRO"
        },
        "condPago" : "CONTADO",
        "fechaEmision" : ISODate("2014-02-20T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-02-20T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 11,
                        "precio" : 18,
                        "producto" : " CORREA 12mm"
                },
                {
                        "cantidad" : 1,
                        "precio" : 490,
                        "producto" : "TALADRO 12mm"
                }
        ],
        "nroFactura" : 1063
}
```
* Obtener las facturas con fecha de emisión posterior al 23/02/2014 y número menor
a 1500. Ordenar por región y cuit del cliente
```sh
db.facturas.find( { fechaEmision: {$gt:ISODate("2014-02-23T00:00:00Z")}, nroFactura: {$lt:1500} }, {} ).sort({"cliente.region": 1, "cliente.cuit":1})
```
```sh
{
        "_id" : ObjectId("55e4a6fcbfc68c676a0410a7"),
        "cliente" : {
                "apellido" : "Manoni",
                "cuit" : 2029889382,
                "nombre" : "Juan Manuel",
                "region" : "NEA"
        },
        "condPago" : "60 Ds FF",
        "fechaEmision" : ISODate("2014-02-25T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-04-26T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 1,
                        "precio" : 700,
                        "producto" : "SET HERRAMIENTAS"
                },
                {
                        "cantidad" : 1,
                        "precio" : 490,
                        "producto" : "TALADRO 12mm"
                }
        ],
        "nroFactura" : 1068
}
...
```
* Obtener sólo los datos de cliente de las facturas donde se haya comprado
“CORREA 10mm”. Ordenar por apellido del cliente.
```sh
db.facturas.find({"item.producto":"CORREA 10mm"},{"cliente":1,"_id":0}).sort({"cliente.apellido":1})
```
```sh
{ "cliente" : { "apellido" : "Zavasi", "cuit" : 2038373771, "nombre" : "Martin", "region" : "CABA" } }
...
```
* Obtener sólo nombre y apellido de cliente, de las facturas con número entre 2500
y 3000.
```sh
db.facturas.find({"nroFactura":{$gt:2500,$lt:3000}},{"cliente.nombre":1,"cliente.apellido":1,"_id":0})
```
```sh

```
* Obtener sólo la fecha de vencimiento de las facturas 5000, 6000, 7000 y 8000.
```sh
db.facturas.find({"nroFactura":{$in:[5000,6000,7000,8000]}},{"fechaVencimiento":1,"_id":0})
```
```sh

```
* Obtener las facturas de los clientes cuyo apellido comience con Z. Ordenar por
número de factura y devolver solo las primeras 5.
```sh
db.facturas.find({"cliente.apellido":/^Z/},{}).sort({"nroFactura":1}).limit(5)
```
```sh
{
        "_id" : ObjectId("55e4a6fbbfc68c676a041064"),
        "cliente" : {
                "apellido" : "Zavasi",
                "cuit" : 2038373771,
                "nombre" : "Martin",
                "region" : "CABA"
        },
        "condPago" : "30 Ds FF",
        "fechaEmision" : ISODate("2014-02-20T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-03-22T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 2,
                        "precio" : 134,
                        "producto" : "CORREA 10mm"
                }
        ],
        "nroFactura" : 1001
}
...
```
* Obtener sólo los números de factura en las que la región sea “CENTRO” o la
condición de pago sea “CONTADO”. Ordenar descendentemente por número de
factura y devolver de la 5 a la 10.
```sh
db.facturas.find({$or:[{"cliente.region":"CENTRO"},{"condPago":"CONTADO"}]},{"nroFactura":1,"_id":0}).sort({"nroFactura":-1}).skip(5).limit(5)
```
```sh
{ "nroFactura" : 1090 }
{ "nroFactura" : 1087 }
{ "nroFactura" : 1086 }
{ "nroFactura" : 1084 }
{ "nroFactura" : 1083 }
```
* Obtener las facturas de todos los clientes que no sean de apellido “Zavasi” ni
“Malinez”.
```sh
db.facturas.find({"cliente.apellido":{$nin:["Zavasi","Malinez"]}},{})
```
```sh
{
        "_id" : ObjectId("55e4a6fcbfc68c676a0410a6"),
        "cliente" : {
                "apellido" : "Lavagno",
                "cuit" : 2729887543,
                "nombre" : "Soledad",
                "region" : "NOA"
        },
        "condPago" : "30 Ds FF",
        "fechaEmision" : ISODate("2014-02-24T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-03-26T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 1,
                        "precio" : 700,
                        "producto" : "SET HERRAMIENTAS"
                }
        ],
        "nroFactura" : 1067
}
...
```
* Obtener sólo el nombre del producto de las facturas donde se haya comprado 15
unidades de dicho producto.
```sh
db.facturas.find({"item.cantidad":15},{"item.producto.$":1,"_id":0})
```
```sh
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
{ "item" : [ { "producto" : "TUERCA 5mm" } ] }
```
* Obtener sólo una factura del cliente de cuit 2038373771, condición de pago “30 Ds
FF” y fecha de vencimiento entre el 20/03/2014 y 24/03/2014.
```sh
db.facturas.findOne({"cliente.cuit":2038373771,"condPago":"30 Ds FF","fechaVencimiento":{$gt:ISODate("2014-03-20T00:00:00Z"),$lt:ISODate("2014-03-24T00:00:00Z")}},{})
```
```sh
{
        "_id" : ObjectId("55e4a6fcbfc68c676a0410a3"),
        "cliente" : {
                "apellido" : "Zavasi",
                "cuit" : 2038373771,
                "nombre" : "Martin",
                "region" : "CABA"
        },
        "condPago" : "30 Ds FF",
        "fechaEmision" : ISODate("2014-02-20T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-03-22T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 2,
                        "precio" : 134,
                        "producto" : "CORREA 10mm"
                }
        ],
        "nroFactura" : 1064
}
```