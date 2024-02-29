# Creando un marco de ejecución
    
Como primer paso para trabajar con el marco [`Executor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html) es crear un objeto de la clase [`ThreadPoolExecutor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadPoolExecutor.html). Se pueden utilizar los cuatro constructores que dispone esta clase o usar la clase factoría [`Executors`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executors.html) que crea objetos `ThreadPoolExecutor`. Cuando disponemos del objeto `Executor`, podemos enviar objetos que implementen la interface `Runnable` o `Callable` para ser ejecutados.

En este ejemplo, aprenderemos los pasos necesarios para la creación de un marco `Executor` y el envío de tareas para su ejecución que simula un servidor web que procesa las peticiones de varios clientes.

1. Como primer paso implementaremos las tareas que deberá ejecutar el servidor web. Para ello creamos una clase llamada `Task` que implementa la interface `Runnable`.

2. Tendremos dos variables de instancia, una que almacena cuando se creó la tarea y otra donde se almacenará el nombre de la tarea. Al constructor de la clase se le pasará el nombre de la tarea y le asignará los valores apropiados a las variables de instancia. Revisar el código de ejemplo del guión.

3. El método `run()` simula la tarea que deberá realizarse. Presentará en la salida estándar el inicio de la tarea. Posteriormente simula el tiempo que lleva realizar la tarea y al finalizar presentará en la salida estándar el tiempo de finalización.

```java
...
/**
 * This method implements the execution of the task. Waits a random period of time and finish
 */
@Override
public void run() {
    System.out.printf("%s: Task %s: Created on: %s\n",Thread.currentThread().getName(),name,initDate);
    System.out.printf("%s: Task %s: Started on: %s\n",Thread.currentThread().getName(),name,new Date());
		
    try {
        Long duration=(long)(Math.random()*10);
        System.out.printf("%s: Task %s: Doing a task during %d seconds\n",Thread.currentThread().getName(),name,duration);
        TimeUnit.SECONDS.sleep(duration);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
		
    System.out.printf("%s: Task %s: Finished on: %s\n",Thread.currentThread().getName(),name,new Date());
}
...
```

4. Ahora implementaremos la clase que representa al servidor que deberá ejecutar las tareas que representan las peticiones de los clientes. El nombre de la clase es `Server`.

5. La clase tendrá una variable de instancia de la clase `ThreadPoolExecutor` que nos permitirá ejecutar las tareas.

6. El constructor de la clase deberá inicializar la variable de instancia, esto es, se creará el objeto `Executor` para que podamos enviarle las tareas que se tendrán que ejecutar. Se ha utilizado la clase factoría `Executors` y que crea un conjunto de hilos con un tamaño no determinado y se creará un nuevo hilo si no hay ninguno disponible en el conjunto de hilos para ejecutar la tarea que se haya añadido.

7. Definimos un método llamado `executeTask(.)` que será el encargado de enviar las tareas al objeto `Executor`. Indicará la recepción de la tarea, para enviar la tarea se debe invocar el método `execute(.)`, que tendrá como argumento la tarea que se desea enviar, del objeto `Executor`. Para finalizar, presentará en la salida estándar información de la tarea enviada. Invocar el método `execute(.)` no implica la inmediata ejecución de la tarea. Será el **marco Executor** el encargado de llevar a cabo las acciones necesarias para su ejecución.

```java
...
/**
 * This method is called when a request to the server is made. The 
 * server uses the executor to execute the request that it receives
 * @param task The request made to the server
 */
public void executeTask(Task task){
    System.out.printf("Server: A new task has arrived\n");
    executor.execute(task);
    System.out.printf("Server: Pool Size: %d\n",executor.getPoolSize());
    System.out.printf("Server: Active Count: %d\n",executor.getActiveCount());
    System.out.printf("Server: Completed Tasks: %d\n",executor.getCompletedTaskCount());
}
...
```

8. También tendremos un método llamado `endServer()` que nos permitirá finalizar el servicio web simulado. Se invocará al método `shutdown()` del objeto `Executor`. De esta forma finalizamos el **marco Executor**. La finalización tiene como consecuencia que ya no podremos enviar más tareas al **marco Executor** pero las tareas pendientes podrán completar su trabajo.

```java
...
/**
 * This method shuts down the executor
 */
public void endServer() {
    executor.shutdown();
}
...
```

9. Para finalizar debemos implementar el método `main(.)` de nuestra aplicación. Para ello crearemos un objeto de la clase `Server`. Luego creamos `100` objetos de la clase `Task` que enviamos al servidor. Para finalizar terminamos con la ejecución del servidor.

```java
/**
 * Main class of the example. Creates a server and 100 request of the Task class
 * that sends to the server
 *
 */
public class Main {

    /**
     * Main method of the example
     * @param args
     */
    public static void main(String[] args) {
        // Create the server
        Server server=new Server();
		
        // Send 100 request to the server and finish
        for (int i=0; i<100; i++){
            Task task=new Task("Task "+i);
            server.executeTask(task);
        }
		
        server.endServer();
    }
}
```


 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY2MDE0MzIwOV19
-->