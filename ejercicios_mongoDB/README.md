Autor: Ing. Martin Acosta - 2020

# Ejercitación MongoDB
## Arquitectura de Datos - CEIoT - FIUBA
## Parte 1
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
## Parte 2
* Obtener los números de factura de los clientes que contengan una i (minúscula o mayúscula) pero que no sea Manoni.
```sh
db.facturas.find({"cliente.apellido":{$regex: 'i',$nin:['Manoni'],$options:'i'}},{_id:0,nroFactura:1})
```
```sh
{ "nroFactura" : 1063 }
{ "nroFactura" : 1064 }
{ "nroFactura" : 1065 }
{ "nroFactura" : 1071 }
...
```
* Obtener las facturas de clientes con apellido terminado en i, cuya región sea CABA y donde hayan comprado una TUERCA, sin importar su medida.
```sh
db.facturas.find({"cliente.apellido":{$regex:'i$'},"cliente.region":"CABA","item.producto":{$regex:'TUERCA'}},{_id:0})
```
```sh
{
        "cliente" : {
                "apellido" : "Zavasi",
                "cuit" : 2038373771,
                "nombre" : "Martin",
                "region" : "CABA"
        },
        "condPago" : "CONTADO",
        "fechaEmision" : ISODate("2014-02-20T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-02-20T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 6,
                        "precio" : 60,
                        "producto" : "TUERCA 2mm"
                },
                {
                        "cantidad" : 12,
                        "precio" : 134,
                        "producto" : "CORREA 10mm"
                }
        ],
        "nroFactura" : 1065
}
...
```
* Obtener las facturas donde se haya comprado una correa, sin importar la medida.
```sh
db.facturas.find({"item.producto":{$regex:'CORREA'}},{_id:0})
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
...
```
* Obtener los datos de cliente de las facturas donde se haya comprado 12 o más de cualquier ítem.
```sh
db.facturas.find({"item.cantidad":{$gte:12}},{_id:0,cliente:1})
```
```sh
{
        "cliente" : {
                "apellido" : "Zavasi",
                "cuit" : 2038373771,
                "nombre" : "Martin",
                "region" : "CABA"
        }
}
{
        "cliente" : {
                "apellido" : "Manoni",
                "cuit" : 2029889382,
                "nombre" : "Juan Manuel",
                "region" : "NEA"
        }
}
...
```
* Obtener las facturas donde no se hayan comprado "SET HERRAMIENTAS"
```sh
db.facturas.find({"item.producto":{$nin:['SET HERRAMIENTAS']}},{_id:0})
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
{
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
...
```
* Armar una función en javascript que recorra un cursor con todos las facturas imprimiendo “Factura <nroFactura>: <cliente.apellido> compró <n> items”
```sh
{
  const cursor = db.facturas.find();
  while(cursor.hasNext()){
    var factura = cursor.next();
    print("Factura "+factura.nroFactura+": "+factura.cliente.apellido+" compró "+factura.item.length+" items");
  }
}
```
```sh
Factura 1063: Malinez compró 2 items
Factura 1067: Lavagno compró 1 items
Factura 1064: Zavasi compró 1 items
Factura 1068: Manoni compró 2 items
Factura 1069: Manoni compró 1 items
Factura 1065: Zavasi compró 2 items
...
```
* Insertar una factura número 999 con usted como cliente, habiendo comprado un Destornillador.
```sh
db.facturas.insert({
        "cliente" : {
                "apellido" : "Acosta",
                "cuit" : 2740488484,
                "nombre" : "Martin",
                "region" : "CABA"
        },
        "condPago" : "CONTADO",
        "fechaEmision" : ISODate("2014-02-20T00:00:00Z"),
        "fechaVencimiento" : ISODate("2014-02-20T00:00:00Z"),
        "item" : [
                {
                        "cantidad" : 1,
                        "precio" : 120,
                        "producto" : " DESTORNILLADOR"
                }
        ],
        "nroFactura" : 999
})
```
```sh
WriteResult({ "nInserted" : 1 })
```
* ¿De qué forma insertará el siguiente array de documentos para que, sin importar si alguno de ellos falla, el resto igualmente se inserte? [{_id:1},{_id:1},{_id:2}]
```sh
db.facturas.insert([{_id:1},{_id:1},{_id:2}],{ordered:false})
```
* Mediante un Bulk agregarle a cada factura del cliente Lavagno agregarle el campo “tipo” con el valor “VIP”. Este deberá estar dentro del campo cliente. (cliente: {nombre:..., apellido:..., tipo:..., ...}). Junto con esto, eliminar las facturas entre 2000 y 2222.
```sh
var bulk = db.facturas.initializeOrderedBulkOp();
bulk.find({"cliente.apellido":"Lavagno"}).update(
        {$set:{"cliente.tipo":"VIP"}},
        {multi:true});
bulk.find({"nroFactura":{$gt:2000,$lt:2222}}).remove()
bulk.execute();
```
```sh
BulkWriteResult({
        "writeErrors" : [ ],
        "writeConcernErrors" : [ ],
        "nInserted" : 0,
        "nUpserted" : 0,
        "nMatched" : 14,
        "nModified" : 14,
        "nRemoved" : 0,
        "upserted" : [ ]
})
```
* Eliminar todas las facturas de los clientes del CENTRO.
```sh
db.facturas.remove({"cliente.region":"CENTRO"})
```
```sh
WriteResult({ "nRemoved" : 15 })
```
* Obtener la factura con el mayor nroFactura del cliente de apellido Lavagno donde haya comprado “SET HERRAMIENTAS” y luego asegurarse de borrar únicamente esa factura.
```sh
db.facturas.findOneAndDelete({"cliente.apellido":"Lavagno","item.producto":"SET HERRAMIENTAS"},{sort:{"nroFactura":-1}})
```
```sh
{
        "_id" : ObjectId("55e4a6fcbfc68c676a0410c2"),
        "cliente" : {
                "apellido" : "Lavagno",
                "cuit" : 2729887543,
                "nombre" : "Soledad",
                "region" : "NOA",
                "tipo" : "VIP"
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
        "nroFactura" : 1095
}
```
* Incrementar el número de factura de la 1501.
```sh
db.facturas.update({"nroFactura":1501},{$inc:{"nroFactura":1}})
```
```sh
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })
```
* A la factura número 1500 cambiarle la condición de pago a “30 Ds FF”
```sh
db.facturas.update({"nroFactura":1500},{$set: {"condPago":"30 Ds FF"}})
```
```sh
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })
```
* Restarle 9 al número de factura de la 1510.
```sh
db.facturas.update({"nroFactura":1510},{$inc: {"nroFactura":-9}})
```
```sh
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })
```
* A la factura número 1515 incrementarle el campo “vistas” y decrementarle el campo “contador”.
```sh
db.facturas.update({"nroFactura":1515},{$inc: {"vistas":1,"contador":-1}})
```
```sh
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
{
        "_id" : ObjectId("5f39990c059ef26e515f2632"),
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
        "nroFactura" : 1515,
        "contador" : -1,
        "vistas" : 1
}
```
* Guarde el documento de la factura número 9000 en una variable. Luego haga un update total de la factura 9000 reemplazándola por el documento {x:Math.random()} (generará un número aleatorio para el campo x). ¿Hay forma de consultar el documento modificado? ¿De qué forma?
```sh
> factura
{
        "_id" : ObjectId("5f399ca9e556dd1ebb6267b0"),
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
        "nroFactura" : 9000
}

> db.facturas.findOne({_id:factura._id})
```
```sh
{ "_id" : ObjectId("5f399ca9e556dd1ebb6267b0"), "x" : 0.6073539426924899 }
```
```sh
La forma de consultar el documento modificado es mediante el id del mismo, ya que al tratarse de una modificación total, 
el campo _id no se ve afectado por el cambio.
```