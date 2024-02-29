# Creando un marco de ejecución con un conjunto de hilos fijo

Cuando trabajamos con el **marco Executor** lo inicializamos con el método `newCachedThreadPool()` de la clase [`Executors`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executors.html), y podemos llegar a tener un problema con el número de hilos que el objeto `Executor` está ejecutando al mismo tiempo. Si no hay un hilo disponible en el **conjunto de hilos** creará uno nuevo para una nueva tarea que haya sido añadida. Si las tareas tienen un tiempo largo de ejecución, es posible que no dispongamos de hilos en el **conjunto de hilos** y tenga que crearse uno nuevo, de esta forma se podría terminar provocando una *sobrecarga del sistema* que hace que el rendimiento de la aplicación no sea apropiado.

Si queremos evitar este problema, la clase `Executors` dispone de un método que crea el conjunto de hilos de un tamaño máximo. Si tenemos más tareas que el número máximo de hilos, el **marco Executor** no crea un nuevo hilo y las tareas quedan pendientes, estarán bloqueadas, hasta que se disponga de un hilo libre. De esta forma podemos controlar los hilos que tiene disponibles una aplicación y garantizar en todo momento un rendimiento apropiado de la misma.

Vamos a modificar el ejemplo anterior para que el marco `Executor` se cree con un número máximo de hilos.

1. Deberemos modificar el constructor de la clase `Server`. Cuando creemos el **marco Executor** invocamos el método `newFixedThreadPool(.)` de la clase `Executors` con el número máximo de hilos que tendrá nuestro conjunto de hilos. En nuestro ejemplo será de `5`.

2. Realizaremos un añadido al método `executeTask(.)` para saber el número de tareas que se han enviado al objeto `Executor`. Revisar el código del ejemplo de la sesión.

```java
...
System.out.printf("Server: Task Count: %d\n",executor.getTaskCount());
...
```

## Información adicional

Otro método de la clase `Executors` es `newSingleThreadExecutor()`. Este es un caso extremo en el que sólo tendremos un hilo disponible para el **conjunto de hilos** del marco `Executor`. De esta forma sólo se podrá realizar una tarea cada vez y el resto quedan pendientes de la finalización de la tarea activa.

## Ejercicios propuestos

- Modificar el ejemplo anterior con el método `newSingleThreadExecutor()` y explicar los resultados obtenidos.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE0Mjk5MDQyM119
-->