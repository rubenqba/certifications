# Proyecto de Certificación

Se desea construir un pequeño e-commerce que permita a los usuarios comprar libros de tecnología. Los principales objetivos de la aplicación son:

1. Garantizar el acceso seguro de los usuarios y sus datos
2. Permitir realizar búsquedas de libros
3. Permitir hacer compras de los productos seleccionados

En la construcción de la aplicación se medirán aspectos como el diseño gráfico, la organizacion o estructura del codigo, asi como la capacidad de que la aplicacion se adapte a diferentes tamaños de pantalla y orientacion. El desarrolloo completo tiene que estar basado en ReactJS.

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
