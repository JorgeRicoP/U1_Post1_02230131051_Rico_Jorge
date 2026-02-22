# U1_Post1_02230131051_Rico_Jorge
# Post-Contenido 1 - Laboratorio
### Parte 1: Análisis del Código Legacy

#### Numero 1
El primer principio que se incumple es el de SRP (Single Responsibility Principle), ya que "OrderManager" cuenta con cuatro responsabilidades distintas dentro de una sola clase, totalmente contrario a lo que el principio expresa, que es que, debe tener una unica razon para cambiar. Al tener multiples responsabilidades dentro de una clase, si es necesario el modificar algun metodo, existe la posibilidad de realizar algun cambio que genere errores accidentales en los otros.

#### Numero 2
El segundo principio que se incumple es el de OCP (Open/Closed Principle) en el metodo de "CreateOrder", ya que si se busca el crear un nuevo descuento, es necesario el modificar el metodo entero, al no existir ningun mecanismo externo de extension. Lo mismo ocurre con las notificaciones, si se busca el enviar un SMS ademas del e-mail, se debe modificar en todo el metodo "CreateOrder"

#### Numero 3
El tercer principio que se incumple es el de DIP (Dependency Inversion Principle), ya que la clase tiene una dependencia concreta instanciada directamente en su interior. En la línea 'new java.io.FileWriter("orders.txt", true)', 'OrderManager' conoce y depende directamente de 'FileWriter', siendo además responsable de construirlo y configurarlo dentro de su propio método. Esto es un incumplimiento porque 'OrderManager' es una clase de alto nivel que contiene lógica de negocio, mientras que 'FileWriter' es una clase de bajo nivel que maneja detalles del sistema de archivos. Al estar atada directamente a esa implementación, cualquier cambio de infraestructura, como migrar de archivos a una base de datos, obliga a modificar la clase de negocio, lo cual DIP prohíbe explícitamente.

#### Numero 4
El cuarto principio que se incumple es el de ISP (Interface Segregation Principle), ya que, al no utilizar interfaces pequeñas y especificas, en caso de que un componente externo quiera usar alguna funcionalidad unica como generar un reporte, este está obligado a depender de todos los metodos de la clase "OrderManager" cuando ni siquiera necesita usarlos. 

### Parte 2 y 3: Refactorización - Separación de Responsabilidades
La refactorización tomó una clase que concentraba toda la lógica del sistema y la dividió en piezas más pequeñas y especializadas. Cada responsabilidad que antes convivía dentro de createOrder, como calcular descuentos, guardar en archivo y enviar notificaciones, fue extraída a su propia clase. Para evitar que estas clases quedaran acopladas entre sí, se introdujeron interfaces que actúan como contratos, permitiendo que OrderService coordine el flujo sin conocer los detalles de cómo se descuenta, cómo se persiste o cómo se notifica. Las implementaciones concretas se ensamblan en un único punto, Main, y se inyectan desde afuera. El resultado es un sistema donde cada clase tiene una razón clara para existir y donde agregar o cambiar funcionalidad no obliga a modificar lo que ya funciona.

### Parte 4: Clean Codey Prueba Final
Para garantizar la calidad del codigo refactorizado se verifico que todos los nombres de clases, métodos y variables expresen claramente su propósito, que cada método del código refactorizado realiza una única operación, la eliminacion de comentarios que simplemente repetían lo que el código ya expresaba por sí mismo y se verificó indentación uniforme, espaciado entre métodos y cumplimiento de las convenciones estándar de Java en todos los archivos del paquete refactored.

### Decisiones de diseño por cada clase
la clase 'Order' se creó para encapsular los datos de un pedido en un objeto propio, eliminando el uso de "String[]" con índices numericos que existia en el codigo general y se aplico SRP ya que su unica responsabilidad es representar los datos de un pedido.

La interfaz 'DiscountStrategy' y sus implementaciones 'StandardDiscount' y 'VIPDiscount' se creó como interfaz y no como clase concreta para cumplir con OCP y DIP. Al ser una abstraccion, permite agregar nuevos tipos de descuento creando una nueva clase que la implemente, sin necesidad de modificar codigo existente. 'OrderService' depende de la interfaz, no de una implementacion especifica.

La interfaz 'OrderRepository' e implementacion 'FileOrderRepository' se extrajo la persistencia a una interfaz para cumplir DIP, desacoplando la logica del negocio del sistema de archivos. Sin el futuro se necesita guardar en una base de datos, basta con crear una nueva implementacion sin tocar 'OrderService'.

La interfaz 'NotificationService' e implementacion 'EmailNotificationService' se creo para abstraer el mecanismo de notificacion, cumpliendo DIP e ISP. 'OrderService' solo conoce el contrato 'notify()', sin saber si la notificacion se envia por email, SMS u otro canal.

La clase 'OrderService' recibe todas sus dependencias por constructor, aplicando inyeccion de dependencias. Esto garantiza que 'OrderService' dependa unicamente de abstracciones y no de implementaciones concretas, cumpliendo DIP y manteniendo SRP al limitarse a coordinar el flujo de creacion de pedidos.

Por ultimo, la clase 'Main' es el unico punto de sistema donde se instancian las implementaciones concretas y se ensamblan las dependencias. Esto mantiene el resto del sistema desacoplado entre si, permitiendo cambiar implementaciones desde un solo lugar.
