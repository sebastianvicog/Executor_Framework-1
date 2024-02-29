

## Sesión 4: Marco Executor (I)

Usualmente, cuando desarrollamos una simple, aplicación concurrente en Java, creamos objetos que implementan la interface [`Runnable`](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html) y luego creamos objetos de la clase [`Thread`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html) para ejecutarlos. Si lo que queremos hacer es desarrollar un programa que ejecute un número elevado de tareas concurrentes, la aproximación anterior tiene las siguientes desventajas:

- Se debe implementar todo el código necesario para poder gestionar los objetos de la clase `Thread` (creación, finalización, obtención de los resultados).

- Se debe crear un objeto de la clase `Thread` por cada una de las tareas. Si se tienen que ejecutar un número elevado de tareas, tiene un impacto negativo en el rendimiento de la aplicación.

- Se tiene que controlar y gestionar eficientemente los recursos del ordenador. Si se crean demasiados hilos, se puede producir saturación en el sistema.
   
Desde Java 5 tenemos a nuestra disposición un mecanismo que nos permite evitar estos problemas. El mecanismo en cuestión se llama el **marco Executor** y se encuentra en la interface [`Executor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html), y sus subinterfaces [`ExecutorService`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html), y la clase [`ThreadPoolExecutor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadPoolExecutor.html) que implementa ambas interfaces.

Este mecanismo separa la creación de las tareas y la ejecución de las mismas. Con un objeto `Executor`, sólo debemos implementar la tarea mediante un objeto que implemente la interface `Runnable` y enviarlo al objeto `Executor`. Él se encargará de la *instanciación* y *ejecución* con los hilos necesarios. El marco `Executor` va más allá y mejora el rendimiento mediante un **conjunto de hilos**. Cuando se envía una tarea al `Executor`, se intenta utilizar uno de los hilos del **conjunto de hilos**, de esta forma se evita estar creando continuamente hilos, es decir, se limita la creación de hilos porque se reutilizan los que ya han terminado con la tarea que tenía previamente asignada.

Otra importante característica del marco `Executor` es la interface [`Callable`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Callable.html). Es similar a la interface `Runnable`, pero ofrece dos mejoras, que son las siguientes:

- El método principal de esta interface, llamado `call()`, puede calcular un resultado.

- Cuando se envía un objeto que implementa la interface `Callable` al *ejecutor*, se obtiene un objeto que implementa la interface [`Future`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html). Se puede utilizar este objeto que controla el estado y el resultado el objeto que implementa la interface `Callable`.

Ahora se presentarán una primera parte sobre las características que nos proporciona esta interface con los siguientes ejemplos:

1. [Creando un **marco de ejecución**.]
2. [Creando un **marco de ejecución** con un conjunto de hilos fijo.]
3. [Ejecutando tareas en un **marco de ejecución** que devuelve un resultado.]
4. [Ejecución múltiple de tareas y procesando el primer resultado.]
5. [Ejecución múltiple de tareas y procesando todos los resultados.]