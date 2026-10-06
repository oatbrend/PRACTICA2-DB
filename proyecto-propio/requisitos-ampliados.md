# Ejercicio 4. Modelo EER 

# ERGON
---

## 4.1 Requisitos ampliados:
---


### La empresa ERGON, perteneciente al sector industrial de especialidades petroleras y aceites, requiere un sistema centralizado para la gestión integral de sus ventas, logística y comercialización de productos. El objetivo principal es consolidar en una base de datos el catálogo de aceites industriales, la cartera de clientes, la red de sucursales de entrega, las empresas transportistas contratadas y el registro detallado de transacciones, garantizando el cálculo automático de montos financieros y la generación de reportes comerciales.
#### Para la organización de su inventario, ERGON comercializa tres líneas principales de aceites industriales: Hyvolt *(ej. Hyvolt I)*, Omnigold *(ej. Omnigold 2000)* e Hyprene *(ej. Hyprene L500)*. En el modelo, la entidad **PRODUCTO** se especializa de forma disjunta (d) y total (t) en estas tres categorías comerciales. De cada producto, sin excepcion, se registra una clave única de 7 dígitos (clave primaria), el costo unitario de producción en USD y el precio unitario de venta en USD.
#### Respecto a la cartera de clientes B2B que adquieren e integran estos insumos, la entidad superclase **CLIENTE** registra atributos como un número único de cliente de 6 dígitos, la razón social y el correo electrónico de contacto principal. Con el fin de gestionar adecuadamente las normativas comerciales y fiscales, CLIENTE se divide en dos subtipos mediante una especialización disjunta (d) y total (t): Cliente Nacional: Almacena el Registro Federal de Contribuyentes (RFC) de 13 dígitos, el régimen fiscal para facturación mexicana y el estado de la república de origen. Cliente de Exportación: Registra el número de identificación fiscal internacional y el país receptor del producto. Esta especialización es disjunta porque un cliente no puede clasificarse simultáneamente como nacional y de exportacion, y es total porque todo cliente registrado debe pertenecer obligatoriamente a una de estas dos categorías.
#### Para la gestión operativa de transacciones, las compras se clasifican mediante la entidad **VENTA / PEDIDO**, la cual se relaciona con CLIENTE a través de la relación <realiza>. Cada pedido registra un folio único (clave primaria), el estatus de la venta (que transita entre pendiente, facturada, entregada y cancelada), una fecha representada como atributo compuesto (día, mes, año), así como el *monto total en USD* y la *utilidad total en USD* 

```bash

montoTotalUSD = Σ subtotales de sus detalles

utilidadTotalUSD = Σ [(precioAplicado - costoProduccion) × cantidad]

```
#### Ambos definidos como atributos derivados. Dado que un pedido abarca múltiples artículos, cada transacción cae en la entidad débil **DETALLE VENTA**, la cual mantiene una dependencia de identificación y existencia con VENTA / PEDIDO mediante la relación identificadora <asocia>. Cada línea de detalle registra una clave parcial, la cantidad de producto, el precio aplicado al momento de la venta, el costo de producción aplicado y el *subtotal* (atributo derivado del producto).

```bash

subtotal = cantidad × precioAplicado

```

#### A su vez, DETALLE VENTA se vincula con la entidad PRODUCTO a través de la relación <aparece>, identificando qué aceite específico fue comercializado en dicho renglón. 

#### En el ámbito logístico, el destino físico de las entregas se administra mediante la entidad débil **SUCURSAL**, la cual depende por existencia de la entidad CLIENTE a través de la relación <tiene> *(un cliente puede poseer una o varias plantas o almacenes de recepción)*. De cada sucursal se requiere una clave parcial de 5 dígitos, el nombre de la planta, el número telefónico del encargado de recepción y la dirección de entrega, modelada como un atributo compuesto (calle, número, ciudad, estado, país y código postal). 

#### Por su parte, el traslado de la mercancía lo realizan empresas de paquetería representadas en la entidad superclase **TRANSPORTISTA**, de la cual se guarda una clave única, el nombre de la empresa, el teléfono de atención y el correo electrónico. Para clasificar sus funciones operativas, TRANSPORTISTA se divide mediante una especialización solapada (o) y total (t) en dos subtipos: Transportista Nacional: Registra el RFC. Por normativa interna, está restringido exclusivamente a realizar trayectos dentro del territorio nacional. Transportista Internacional: Registra el código aduanal y la agencia aduanal asociada. La especialización es solapada porque un transportista internacional certificado posee la capacidad legal y operativa de realizar también entregas dentro del país (pudiendo pertenecer a ambos subtipos simultáneamente si brinda ambos servicios), mientras que un transportista nacional carece de credenciales aduanales para realizar exportaciones. Asimismo, es total porque cualquier empresa de transporte registrada debe pertenecer al menos a una de las dos clasificaciones.

#### Finalmente, el evento de envio fisico del pedido se modela a traves de la relacion ternaria <envío>, la cual conecta en una sola acción operativa a VENTA / PEDIDO, SUCURSAL y TRANSPORTISTA. De este vinculo ternario emergen atributos propios del proceso de envio: la fecha de despacho, el número de guía de rastreo y el costo del flete.

---

## JUSTIFICACION EXTENDIDA

#### En el modelo EER de ERGON se implementaron entidades debiles, especializaciones y una relacion ternaria para representar mejor las reglas de la empresa.

#### *Entidades débiles:*
#### Las entidades DETALLE VENTA y SUCURSAL son debiles porque dependen de otra entidad para existir. Un detalle de venta no puede existir si no existe primero un pedido, ya que representa un articulo dentro de ese pedido. Por eso su identificacion depende de VENTA / PEDIDO.

#### En el caso de SUCURSAL, esta depende de CLIENTE, porque una sucursal representa una planta o almacen que pertenece a un cliente. Por si sola no tendria sentido dentro del sistema. Además, su clave parcial solamente es unica dentro de cada cliente.

#### *Especializaciones:*
#### En PRODUCTO se utilizo una especialización disjunta y total, porque cada producto debe pertenecer a una sola linea: Hyvolt, Omnigold o Hyprene.

#### En CLIENTE tambien es disjunta y total, porque cada cliente debe ser nacional o de exportacion, pero no puede ser ambos.

#### En TRANSPORTISTA se utilizo una especialización solapada y total, porque una empresa puede tener capacidad tanto para transporte nacional como internacional. Por eso puede pertenecer a los dos subtipos al mismo tiempo.

#### *Cardinalidades:*
#### Las cardinalidades representan como funcionan las operaciones de ERGON. Un cliente puede realizar varios pedidos, pero cada pedido pertenece a un cliente. Un pedido puede tener varios detalles y cada detalle pertenece a un pedido. Un producto puede aparecer en muchos detalles y un cliente puede tener varias sucursales.

#### La relación ternaria <envío> se utiliza porque un envío relaciona al mismo tiempo tres elementos: el pedido, la sucursal de destino y el transportista. Además, permite guardar datos propios del envío, como la fecha de despacho, la guía y el costo del flete.

#### 3 conusltas que este modelo permite responder 

1. Cuanto se vendio y cuanto se gano en un pedido:

    Se pueden consultar los detalles, cantidades, precios, subtotales, calcular el monto y la utilidad total.

2. La informacion fiscal de cada cliente:

    Se puede identificar si es nacional o de exportación y consultar los datos específicos de cada tipo, 
como RFC, régimen fiscal o país receptor.

3. Quien, cuando y donde se entrego un pedido:

    Se puede conocer la sucursal de destino, el transportista utilizado, la fecha de despacho, el numero de guia y el costo del flete.

---

## draw.io

#### Para la elaboracion de los modelos en notacion de Peter Chen y Crow's Feet, se decidio usar la herramienta de draw.io, debido a que tiene una interfaz muy intuitiva y cuenta con todos los elementos necesarios para la construccion de los diagramas siguiendo la simbologia de cada notacion requerida. Previamente en la Practica 1 "Modelo Entidad Relacion" se utilizo y no se tuvo algun tipo de complicacion con el resultado. 