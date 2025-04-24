Objetivo: Aplicar múltiples patrones de diseño (Strategy, Builder, Composite, Adapter y Command) en un sistema integrado para gestionar pedidos en un restaurante.

Descripcion
El restaurante necesita modernizar su sistema de gestión de pedidos. 
Actualmente, el proceso es manual y propenso a errores.
Se desea una solución de software que permita:

-Crear pedidos personalizados con combinaciones variables de platos, bebidas y extras.
-Aplicar descuentos según el tipo de cliente o promociones temporales.
-Gestionar el flujo de pedidos: enviar a cocina, cancelar o modificar órdenes.
-Integrar con el sistema de cocina (un sistema de terceros), que usa un formato de datos distinto al del área de atención al cliente.

Requerimientos:
Construcción flexible de pedidos:   

      Un pedido puede incluir múltiples items (plato principal, acompañamientos, bebidas, extras).
      Algunos items son obligatorios (ej: plato principal), otros opcionales.
      Debe soportar combos predefinidos (ej: "Menú infantil" = hamburguesa + papas + juguete).

Cálculo de precios con descuentos:

      Descuento del 10% para clientes frecuentes.
      Promoción "Martes de Pasta": 15% de descuento en pastas los martes.
      Los descuentos no son acumulables.

Gestión de acciones:
      Enviar pedido a cocina.
      Cancelar pedido (con confirmación).
      Posibilidad de deshacer la última acción.

Comunicación con cocina:
      El sistema de cocina espera los pedidos en XML, pero la interfaz de atención al cliente trabaja con JSON.
      La conversión debe ser automática y transparente para el usuario.



Para esta solución, se opto por utilizar el Patrón Adapter, Strategy y el Patron Builder.
El patron Adapter se utiliza para la creación de los distintos pedidos por medio de la funcionalidad que ofrece builder, en este caso cada Pedido esta compuesto de un item, que este, en base a un tipo, se identifica para saber si es un plato principal, bebida, etc. Esto es útil para poder ir creando pedidos con distintos items sin necesidad de crear constructores por cada caso.  Este patrón ayuda a separar la construcción del pedido de su representación final. Con el Builder.

La clase PedidoBuilder tiene metodos como setCliente(), agregarItem(), aplicarDescuento(), y construir(), que permiten crear un Pedido de manera un poco mas flexilbe y controlada. Esto facilita la creacion de pedidos personalizados, ya que cada componente del pedido puede ser añadido de manera secuencial.
Esto también permite crear distintos tipos de pedidos sin sobrecargar el constructor del Pedido con demasiados parametros.

El Patrón Strategy permite cambiar el comportamiento de los distintos descuentos de manera mas dinamica, sin modificar el código principal. Al tener una jerarquía de clases de descuento, es posible agregar nuevos tipos de descuento de forma facil sin modificar el sistema que los utiliza.
Para ello se penso que se define una interfaz Descuento que tiene un método aplicarDescuento(), se crean clases concretas como DescuentoFrecuente, DescuentoMartesPasta, DescuentoSinEfecto, que implementan la lógica específica de cada descuento.
El Pedido puede tener una referencia a un Descuento, lo que permite elegir que estrategia aplicar al calcular el total.

El sistema de atención al cliente trabaja con JSON, pero el sistema de cocina espera los pedidos en XML. Entonces una manera de convertir de manera automatica el formato de los datos sin que el sistema de atención al cliente se vea afectado por la complejidad de la conversión.
Es entonces que el patron Adapter permite los dos sisteams puedan estar comunicados. En este caso, el CocineroAdapter es basicamente como un puente entre el sistema de atencion al cliente  y el sistema de cocina.
Para esto el CocineroAdapter tiene un método convertirAxml y convertirAJson, que toma un Pedido en formato JSON y lo convierte a XML y viceversa por si es neceario su uso. El Pedido se mantiene en JSON durante su creacion y es convertido cuando se envía al sistema de cocina.
