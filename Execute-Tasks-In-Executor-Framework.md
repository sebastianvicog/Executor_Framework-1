# Ejecutando tareas en un marco de ejecución que devuelve un resultado
    
Una ventaja del marco [`Executor`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html) es que se pueden ejecutar tareas concurrentes que devuelven un resultado. Para ello Java nos proporciona las siguientes interfaces:

- [`Callable`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Callable.html): Esta interface tiene el método `call()`. En este método, se implementa la lógica de la tarea. La interface `Callable` es una *interface parametrizada*, significa que se debe indicar el tipo de dato que el método `call()` podrá calcular.

- [`Future`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html): Esta interface tiene métodos que permiten obtener el resultado generado por un objeto que implementa la interface `Callable` y tratar su estado.

En este ejemplo se presentan tareas que calcularán un resultado y que serán ejecutadas por el **marco Executor**.

1. Definimos una clase llamada `FactorialCalculator` que implementa la interface `Callable` *parametrizada* con el tipo `Integer`.

2. Tendremos un objeto de la clase `Integer` que obtendrá su valor del constructor de la clase.

3. Ahora implementamos el método `call()` que realizará el cálculo del factorial de la variable de instancia de la clase. Cuando finalice el cálculo presenta el resultado en la salida estándar y para finalizar devolverá el valor calculado.

```java
...
/**
 * Method called by the executor to execute this task and calculate the factorial of a
 * number
 */
@Override
public Integer call() throws Exception {
    int num, result;
		
    num=number.intValue();
    result=1;
		
    // If the number is 0 or 1, return the 1 value
    if ((num==0)||(num==1)) {
        result=1;
    } else {
        // Else, calculate the factorial
        for (int i=2; i<=number; i++) {
            result*=i;
            Thread.sleep(20);
        }
    }
    System.out.printf("%s: %d\n",Thread.currentThread().getName(),result);
    // Return the value
    return result;
}
...
```

4. Ahora implementamos el método `main()` de la aplicación. Tendremos una variable que representa el **marco Executor** que crearemos con un tamaño máximo de `2` hilos.

5. Necesitaremos una lista para almacenar los resultados que devuelven nuestras tareas. Esta lista estará *parametrizada* por la interface `Future` y el tipo `Integer` que es lo que devuelven nuestras tareas.

6. El siguiente paso es crear `10` tareas que calcularán el factorial para `10 Integer` aleatorios. Para realizar el cálculo se añadirán al objeto `Executor` mediante el método `submit()` y almacenaremos el resultado en la lista de resultados creada anteriormente.

```java
...
// Create and send to the executor the ten tasks
for (int i=0; i<10; i++){
    Integer number=new Integer(random.nextInt(10));
    FactorialCalculator calculator=new FactorialCalculator(number);
    Future<Integer> result=executor.submit(calculator);
    resultList.add(result);
}
...
```

7. Ahora estaremos en un bucle hasta que se completen todas las tareas del **marco Executor**, en nuestro ejemplo `10`, y en cada ejecución presentaremos el estado de cada una de las tareas. Es decir, si se ha completado o no la tarea.

```java
...
// Wait for the finalization of the ten tasks
do {
    System.out.printf("Main: Number of Completed Tasks: %d\n",executor.getCompletedTaskCount());
    for (int i=0; i<resultList.size(); i++) {
        Future<Integer> result=resultList.get(i);
        System.out.printf("Main: Task %d: %s\n",i,result.isDone());
    }
    try {
        Thread.sleep(50);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
} while (executor.getCompletedTaskCount()<resultList.size());
...
```

8. Para finalizar se presentará en la salida estándar el resultado del cálculo del factorial de cada una de las tareas y se terminará la ejecución del **marco Executor**.

```java
...
// Write the results
System.out.printf("Main: Results\n");
for (int i=0; i<resultList.size(); i++) {
    Future<Integer> result=resultList.get(i);
    Integer number=null;
    try {
        number=result.get();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (ExecutionException e) {
        e.printStackTrace();
    }
    System.out.printf("Core: Task %d: %d\n",i,number);
}
		
// Shutdown the executor
executor.shutdown();
...
```


 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTMzMjU3NDM0MCwtMzIwMjU2NDUwLC0xND
k5NDg5NTRdfQ==
-->