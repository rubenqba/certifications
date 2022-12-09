# Bookstore

Nivel de dificultad: medio

Construir un pequeño e-commerce que permita a los usuarios comprar libros de tecnología. Los principales objetivos de la aplicación son:

1. Garantizar el acceso seguro de los usuarios y sus datos
2. Permitir realizar búsquedas de libros
3. Permitir hacer compras de los productos seleccionados

En la construcción de la aplicación se medirán aspectos como el diseño gráfico, la organizacion o estructura del codigo, asi como la capacidad de que la aplicacion se adapte a diferentes tamaños de pantalla y orientacion. El desarrollo completo tiene que estar basado en ReactJS. Aunque no es objetivo principal, se valora tambien el uso de buenas practicas de desarrollo.

## Rutas definidas

La aplicación tendrá como rutas predefinidas las siguientes:

- `/`: es la ruta de acceso a la aplicacion, se deja a consideracion del aspirante su diseño y funcionalidad
- `/books`: es la ruta por defecto (home) en la misma se mostrarán los últimos libros publicados
- `/books/{book_id}`: en esta ruta se mostrará la información específica de un libro por su ID (vista detail)
- `/users/{user_id}`: esta ruta mostrará el perfil de usuario por su ID
- `/users/{user_id}/cart`: esta ruta mostrará el carrito de compras de un usuario por su ID
- `/checkout/{user_id}`: esta ruta contendrá el flujo para realizar el pago de los productos seleccionados por ID de usuario

### Autenticación y autorización

Para la autenticación de usuarios se utilizará el estándar OAuth2. El mismo será soportado por un servidor [Keycloak](https://www.keycloak.org/) del que se proveerán las credenciales para su uso al email de contacto registrado por el aspirante.

El dominio de autenticación que será puesto a su disposición tendrá dos posibles escenarios: uno donde el cliente se registra con usuario y contraseña; y otro donde hace uso del acceso a **Google** para registrarse. Para ambos casos el servicio de **Keycloak** estará configurado para brindar esta funcionalidad. Usted solo debe encargarse de la integración de esta en la aplicación.

Como restricción no se permite que ningun usuario pueda acceder a informacion de otro usuario.

### Módulo de usuario

El modulo de usuarios contará con dos vistas: una para visualizar la información del perfil y otra para el carrito de compras.

#### Perfil de usuario

Se mostrarán los datos personales capturados en el `idToken` que se obtiene a partir de la autenticación. Al menos los siguientes atributos deben aparecer. El aspirante puede agregar a la vista los que considere necesarios.

- nombre completo del usuario
- email
- avatar

Tenga en cuenta que atributos como el `avatar` pueden ser nulos o no contener informacion, en cuyo caso debe mostrar una imagen con las iniciales del usuario.

#### Carro de compras

En esta vista se mostrarán todos los productos que el usuario ha seleccionado para su posible compra. Se mostrarán en modo de lista donde al menos aparezca la siguiente informacion:

- por cada producto:
  - nombre
  - cantidad
  - precio unitario
  - subtotal
- en el sumario de la lista:
  - cantidad total de productos
  - cantidad total de items
  - precio total

La vista tiene que incluir un accion que inicie el pago de los productos (véase [Modulo de checkout](#módulo-de-checkout)).

La integración de este modulo se realizará a partir de las operaciones exportadas por un servicio que se desarrolló especialmente para este ejercicio y que se encuentra disponible en la siguiente dirección [https://bookstore-api.gyfted.dev/api/cart](https://bookstore-api.gyfted.dev/api/cart).

El servicio cuenta con 4 operaciones:

- `GET    /api/cart`: obtener el carro de compras para el usuario actual
- `POST   /api/cart/book`: permite agregar o modificar los productos en el carro de compras
- `DELETE /api/cart/book/:book_id`: elimina el producto con identificador `book_id` del carro de compras
- `POST   /api/cart`: completa o cierra un carro de compras, esta accion se supone se ejecute al completar el checkout

En el caso de agregar un producto al carrito, se espera el siguiente body como parte del request:

```json
{
  "product": "9781491954249",
  "title": "Designing Across Senses",
  "subtitle": "A Multimodal Approach to Product Design",
  "image": "https://itbook.store/img/books/9781491954249.png",
  "price": 2.99,
  "qty": 1
}
```

Los atributos requeridos son: `product`, `title`, `price` y `qty`. 

En el caso de la operación de completar y cerrar el carrito de compras, no es necesario enviar un body. 

Todos las operaciones anteriormente descritas requieren del uso de autenticación mediante el **access token** JWT obtenido mediante la [autenticacion del usuario](#autenticación-y-autorización). Para ayuda en las especificaciones de cada una de estas operaciones puede consultar la sección de [Ayuda y Sugerencias](#ayuda-y-sugerencias)

### Módulo de libros

Para consultar la informacion de los libros se utilizará la API de [**ItEbooks**](https://api.itbook.store/). Dicha API cuenta con los recursos necesarios para mostrar y consultar la información que se requiere.

#### Vista principal

En esta vista se mostrarán la lista de libros y además contará con una opcion de búsqueda. Cada libro deberá contener titulo, subtitulo, una imagen de la portada, su codigo ISBN y su precio.

En esta vista la información puede obtenerse de dos maneras según sea el caso. En un momento inicial cuando la vista carga se consultarán y visualizarán los últimos libros publicados. Si el usuario decide usar la opcion de busqueda, entonces en ese caso se consultarán y visualizarán los datos de los libros que resulten semejantes.

Si se hace click sobre algun libro, se pasará a la vista de detalle del libro seleccionado (véase: [Vista detalle](#vista-detalle))

#### Vista detalle

El requerimiento fundamental de esta vista es mostrar la informacion detallada de cada libro. Aquí se incluirán los siguientes atributos:

- titulo
- subtitulo
- autor
- editorial
- codigo ISBN
- año de publicacion
- precio
- descripcion del contenido
- rating

Adicionalmente se tendrán acciones para adicionar al carrito el producto actual tantas veces como desee el usuario. En caso que este producto exista en el carrito se sumarán las cantidades seleccionadas y la existente en el carro de compras.

### Módulo de checkout

En este modulo cuenta con 3 vistas a modo de pasos secuenciales (steps, o wizard). Las 3 vistas a tener en cuenta son:

1. detalle de la compra
2. datos de pago
3. confirmacion y agradecimientos

El usuario podrá ir adelante y hacia atrás siempre que no haya confirmado la compra.

#### Detalle de compra

El detalle de la compra tiene que incluir un sumario de los productos que el usuario seleccionó. Se mostrarán los productos y sus cantidades, así como los respectivos calculos parciales y totales del pago a realizar. Esta vista es perfectamente fusionable con el carrito de compras. Se deja a consideración del aspirante su implementación.

#### Datos de pago

Para capturar los datos de pago se utilizará la integracion con [Stripe](https://stripe.com/en-mx). Se proveerá a los aspirantes las llaves de acceso de una cuenta de pruebas para que puedan ejecutar tantas compras como deseen usando dicha integracion. El formulario básico debe registrar:

1. los datos del medio de pago,
2. datos de la compra (cantidad de productos y cantidad total de items) producto y
3. el email del usuario

Tenga en consideracion que muchos de estos datos ya se tienen al momento de la compra y pueden ser registrados como _medatatos_ en Stripe al momento de crear la intención de pago.

#### Confirmación y agradecimientos

Una vez que se realice el pago, se debe informar al usuario del resultado del mismo. En caso que sea exitoso mostrar un mensaje en refencia y dar la opcion de que el usuario navege hasta la vista principal de la aplicación (véase [Vista principal](#vista-principal)). En caso de error, mostrar un mensaje con el error recibido y dar la opción de regresar al carro de compras para intentarlo nuevamente.

## Ayuda y sugerencias

### Colecciones de prueba

Los archivos [bookstore.postman_collection.json](bookstore.postman_collection.json) y [bookstore.postman_environment.json](bookstore.postman_environment.json) que se incluyen en este repositorio contienen una colección de [Postman](https://www.postman.com) permiten la verificación de las operaciones así como ejemplos de las peticiones.

Puede ejecutarlos mediante la herramienta Newman de la siguiente manera:

```bash
newman run bookstore.postman_collection.json -e bookstore.postman_environment.json
```

El resultado será una salida similar a esta:

```bash
$ newman run bookstore.postman_collection.json -e bookstore.postman_environment.json
newman

Bookstore

→ get current user shopping cart
  POST https://auth.gyfted.io/realms/certification/protocol/openid-connect/token [200 OK, 4.32kB, 624ms]
  GET https://bookstore-api.gyfted.dev/api/cart [200 OK, 762B, 506ms]

→ add book to shopping cart
  POST https://bookstore-api.gyfted.dev/api/cart/book [202 Accepted, 789B, 533ms]
  ✓  Status code is Accepted
  ✓  Shopping Cart status is pending
  ✓  Have book in shopping cart
  ✓  Total is greater than zero

→ delete book from shopping cart
  DELETE https://bookstore-api.gyfted.dev/api/cart/book/9781617291609 [202 Accepted, 702B, 297ms]
  ✓  Status code is Accepted
  ✓  Shopping Cart status is pending

→ close shopping cart
  POST https://bookstore-api.gyfted.dev/api/cart [202 Accepted, 791B, 292ms]
  ✓  Status code is Accepted
  ✓  Have books in shopping cart
  ✓  Total is greater than zero
  ✓  Shopping Cart status is completed

┌─────────────────────────┬─────────────────────┬────────────────────┐
│                         │            executed │             failed │
├─────────────────────────┼─────────────────────┼────────────────────┤
│              iterations │                   1 │                  0 │
├─────────────────────────┼─────────────────────┼────────────────────┤
│                requests │                   5 │                  0 │
├─────────────────────────┼─────────────────────┼────────────────────┤
│            test-scripts │                   7 │                  0 │
├─────────────────────────┼─────────────────────┼────────────────────┤
│      prerequest-scripts │                   4 │                  0 │
├─────────────────────────┼─────────────────────┼────────────────────┤
│              assertions │                  10 │                  0 │
├─────────────────────────┴─────────────────────┴────────────────────┤
│ total run duration: 2.4s                                           │
├────────────────────────────────────────────────────────────────────┤
│ total data received: 4.82kB (approx)                               │
├────────────────────────────────────────────────────────────────────┤
│ average response time: 450ms [min: 292ms, max: 624ms, s.d.: 133ms] │
└────────────────────────────────────────────────────────────────────┘

$
```
