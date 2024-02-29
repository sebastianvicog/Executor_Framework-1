# Ejecución múltiple de tareas y procesando el primer resultado

Un problema común en la programación concurrente es cuando varias tareas concurrentes resuelven un problema, y sólo estamos interesados en el primer resultado de esas tareas. Por ejemplo, queremos **ordenar un array**. Disponemos de diferentes algoritmos de ordenación. Ejecutamos todos ellos y nos quedamos con el resultado del primero que termine, esto es, el algoritmo que sea más rápido en **ordenar el array**.

En este ejemplo vamos a simular que un usuario debe ser validado mediante dos métodos diferentes. El usuario quedará validado si uno de los métodos de validación lo hace.

1. Definimos una clase llamada `UserValidator` que implementa el proceso de validación.

2. Tendremos una variable de instancia para almacenar el proceso de validación. En el constructor se dará el valor apropiado a esa variable.

3. Implementamos el método `validate(.)` al que le pasamos el nombre y clave del usuario a validar. Simulamos el tiempo que consume el proceso de validación. Para finalizar devolveremos aleatoriamente `true` o `false` como resultado simulado de la validación.

```java
...
/**
 * Method that validates a user
 * @param name Name of the user
 * @param password Password of the user
 * @return true if the user is validated and false if not
 */
public boolean validate(String name, String password) {
    // Create a new Random objects generator
    Random random=new Random();
		
    // Sleep the thread during a random period of time
    try {
        Long duration=(long)(Math.random()*10);
        System.out.printf("Validator %s: Validating a user during %d seconds\n",this.name,duration);
        TimeUnit.SECONDS.sleep(duration);
    } catch (InterruptedException e) {
        return false;
    }
		
    // Return a random boolean value
    return random.nextBoolean();
}
...
```

4. Ahora definimos la clase `TaskValidator` que implementa la interface `Callable` *parametrizada* por el valor devuelto `String`. Esta clase simulará la tarea de validación de un usuario.

5. Tendremos como variables de instancia:
	- Un objeto de la clase `UserValidator` para representar el método de validación.
	- El nombre de usuario a validar.
	- La clave del usuario a validar.

Mediante el constructor estableceremos los valores para las variables de instancia. Consultar el código del ejemplo del guión.

6. Implementamos el método `call()` que implementa la tarea de validación. Si no se puede validar el usuario mediante el método de validación, presenta el mensaje en la salida estándar y crea una excepción con el mensaje que indica que no se ha validado el usuario.

7. En otro caso, indica que el usuario ha sido validado y devuelve el nombre del método de validación como resultado de la finalización de la tarea.

```java
...
/**
 * Core method of the Callable interface. Tries to validate the user using the user
 * validation system. If the user is validated, returns the name of the validation system. 
 * If not, throws and Exception
 * @return The name of the user validation system.
 * @throws Exception An exception when the user is not validated
 */
@Override
public String call() throws Exception {
    if (!validator.validate(user, password)) {
        System.out.printf("%s: The user has not been found\n",validator.getName());
        throw new Exception("Error validating user");
    }
    System.out.printf("%s: The user has been found\n",validator.getName());
    return validator.getName();
}
...
```

8. Ahora implementamos el método `main(.)` de nuestra aplicación. Crearemos un usuario y clave simulados. Creamos también dos objetos de la clase `UserValidator` para simular dos métodos diferentes de validación del usuario.

9. Crearemos dos objetos de la clase `TaskValidator` para validar el usuario con uno de los métodos de validación. Añadiremos los dos objetos de la clase `TaskValidator` a una lista. Creamos un objeto de la clase [`ThreadPoolExecutor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadPoolExecutor.html) para nuestro **marco Executor**.

10. Ahora utilizamos el método `invokeAny(.)` del objeto `Executor` al que pasaremos la lista con las tareas a ejecutar. Almacenamos el resultado, si se ha conseguido una validación, en un objeto de la clase `String`. En caso contrario, si ningún método ha conseguido la validación, se mostrará la excepción que se lanza por los procesos de validación. Para finalizar terminamos con el **marco Executor**.

```java
...
// Create a new Executor
ExecutorService executor=(ExecutorService)Executors.newCachedThreadPool();
String result;
try {
    // Send the list of tasks to the executor and waits for the result of the first task 
    // that finish without throw and Exception. If all the tasks throw and Exception, the
    // method throws and ExecutionException.
    result = executor.invokeAny(taskList);
    System.out.printf("Main: Result: %s\n",result);
} catch (InterruptedException e) {
    e.printStackTrace();
} catch (ExecutionException e) {
    e.printStackTrace();
}
		
// Shutdown the Executor
executor.shutdown();
System.out.printf("Main: End of the Execution\n");
...
```


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUzNTU5NjY5NCwtNjc1OTIwOF19
-->