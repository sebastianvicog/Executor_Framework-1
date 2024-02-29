# Ejecución múltiple de tareas y procesando todos los resultados
    
El **marco Executor** permite ejecutar tareas concurrentes sin que nos tengamos que preocupar sobre la creación y ejecución de los hilos. Nos proporciona la interface [`Future`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html) que nos permite el control del estado y obtener los resultados de cualquier tarea que se ejecuta en el **marco Executor**.

Cuando queremos esperar a la finalización de una tarea, podemos utilizar alguno de los siguientes dos métodos:

- El método `isDone()` de la interface `Future` devuelve un valor `true` si la tarea ha finalizado su ejecución.

- El método `awaitTermination(.)` de la clase [`ThreadPoolExecutor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadPoolExecutor.html) que bloquea la ejecución del hilo hasta que todas las tareas han finalizado su ejecución después de la llamada del método `shutdown()`, ya que no se pueden añadir más tareas al **marco Executor** pero no espera la finalización de las tareas pendientes.
    
Ambos métodos tienen inconvenientes. Con el primero, sólo podemos controlar si la tarea se ha completado, y con el segundo, se debe finalizar el **marco Executor** para esperar a la finalización de todas las tareas, en otro caso la llamada al método tiene un retorno inmediato.

La clase `ThreadPoolExecutor` proporciona un método para enviar una lista de tareas al **ejecutor** y esperar hasta la finalización de todas las tareas de la lista. En este ejemplo aprenderemos como utilizar esta característica. Tendremos tres tareas que se ejecutan concurrentemente y se esperará a que finalicen todas ellas antes de presentar los resultados en la salida estándar.

1. Definimos una clase llamada `Result` que nos permitirá almacenar el resultado de las tareas concurrentes que vamos a ejecutar.

2. La clase tendrá como variables de instancia:
	- Un objeto `String` que almacenará el nombre de la tarea.
	- Un valor entero que almacena el resultado de la tarea.
    
3. Implementamos los métodos de acceso para las variables de instancia y como constructor dejamos el constructor por defecto. Consultar el código de ejemplo del guión de prácticas.

4. Definimos una clase llamada `Task` que implementa la interface `Callable` *parametrizada* como valor devuelto por la clase `Result`.

5. Tendremos una variable de instancia de la clase `String` para almacenar el nombre de la tarea. Implementamos un constructor para asignar el valor de esta variable de instancia. Revisar el código de ejemplo del guión de prácticas.

6. Implementamos el método `call()` para establecer lo que debe realizar la tarea. Como primer paso, presentamos en la salida estándar el nombre de la tarea. Generamos un valor aleatorio que simulará el tiempo que llevará la tarea y lo comunicamos por la salida estándar.

7. Para finalizar la tarea creamos un objeto de la clase `Result` donde se almacenará el nombre de la tarea y el resultado obtenido de la misma, una suma de `5` números aleatorios, y devolvemos el resultado como finalización de la tarea.

```java
...
/**
 * Main method of the task. Waits during a random period of time and then
 * calculates the sum of five random numbers
 */
@Override
public Result call() throws Exception {
    // Writes a message to the console
    System.out.printf("%s: Staring\n",this.name);
		
    // Waits during a random period of time
    try {
        Long duration=(long)(Math.random()*10);
        System.out.printf("%s: Waiting %d seconds for results.\n",this.name,duration);
        TimeUnit.SECONDS.sleep(duration);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }		
		
    // Calculates the sum of five random numbers
    int value=0;
    for (int i=0; i<5; i++){
        value+=(int)(Math.random()*100);
    }
		
    // Creates the object with the results
    Result result=new Result();
    result.setName(this.name);
    result.setValue(value);
    System.out.printf("%s: Ends\n",this.name);

    // Returns the result object
    return result;
}
...
```

8. Ahora implementamos el método `main()` de nuestra aplicación Java. Tendremos una objeto para el **marco Executor**, utilizaremos el método `newCachedThreadPool()`.

9. Creamos una lista para almacenar tres tareas de la clase `Task`.

```java
...
// Create an executor
ExecutorService executor=(ExecutorService)Executors.newCachedThreadPool();

// Create three tasks and stores them in a List
List<Task> taskList=new ArrayList<>();
for (int i=0; i<3; i++){
    Task task=new Task("Task-"+i);
    taskList.add(task);
}
...
```

10. Creamos una lista, `Future`, para almacenar los resultados devueltos por las tareas *parametrizada* con el valor devuelto, `Result`, que inicialmente se encuentra vacía, con el valor `null`.

11. Para esperar a que todas las tareas completen su trabajo debemos añadirlas al **marco Executor** mediante el método `invokeAll(.)` que le pasaremos la lista de tareas que queremos ejecutar.

```java
...
// Call the invokeAll() method
List<Future<Result>>resultList=null;
try {
    resultList=executor.invokeAll(taskList);
} catch (InterruptedException e) {
    e.printStackTrace();
}
...
```

12. Como no vamos a ejecutar más tareas, finalizamos el **marco Executor** mediante el método `shutdown()`.

13. Para finalizar, y una vez que se han completado todas las tareas, presentamos los resultados de todas ellas en la salida estándar.

```java
...
// Finish the executor
executor.shutdown();
		
// Writes the results to the console
System.out.printf("Core: Printing the results\n");
for (int i=0; i<resultList.size(); i++){
    Future<Result> future=resultList.get(i);
    try {
        Result result=future.get();
        System.out.printf("%s: %s\n",result.getName(),result.getValue());
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    } 
}
...
```

## Información adicional

- Revisar el libro *Java 7 Concurrency Cookbook*,  se encuentra disponible para los alumnos de la [Universidad de Jaén](https://www.ujaen.es/) por medio de su servicio de [Biblioteca Digital](http://www.ujaen.debiblio.com/login?url=https://learning.oreilly.com/home/), para más información relativa a todos los ejemplos presentados en el guión.

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM3MjE2NTM2OV19
-->