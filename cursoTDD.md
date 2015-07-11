# Curso TDD
## Juan Diego
**Test unitarios en Java**
> "Yo sin pruebas me siento desnudo". La inseguridad aparece cuando estás acostumbrado y no tienes pruebas.

# Indice de contenidos
- [Objetivo](#def-objetivo)
- [Contexto de las Pruebas de Software](#def-contexto)
- [Tipos de Pruebas](#def-tipos de prueba)
- [Quality Assurance](#def-quality assurance)
- [Pruebas Unitarias JUnit](#def-pruebas unitarias)
- [Mockito](#def-mockito)
- [EasyMock](#def-easy mock)
- [Cobertura](#def-cobertura)
- [Aproximación Progresiva](#def-aproximacion progresiva)
- [Medida de la Calidad del Código](#def-medida calidad)
- [Más Allá](#def-mas alla)
- [Pruebas Funcionales](#def-pruebas funcionales)
- [Anexos](#def-anexos)

<a name="def-objetivo"></a>
# Objetivo
- Cambiar mentalidad de programar código. Orientado a Tests.
- Desarrollar conocimientos para dotar a un proyecto de un conjunto de tests

<a name="def-contexto"></a>
# 1. Contexto de las Pruebas de Software

La prueba es un proceso que ayuda a determinar la exactitud, la integridad y la calidad.
Hay que prever todas las posibles ramas del error para llegar a un código que haga lo que yo quiero que haga y haya la menor incertidumbre posible.

## ¿Por qué hacer pruebas?
Desde nuestro punto de vista:
- Por que somos humanos y cometemos errores (código, comprensión del problema).
- Para desarrollar otro módulo el resto debe tener un correcto funcionamiento.
- SW con fallos = inutilizable + mala imagen de empresa y desarrolladores
- Pueden producir pérdidas de vidas/dinero en  ciertos sistemas críticos.

Desde el punto de vista del Cliente:
- Porque no quieren pagar un SW que no funciona o lo hace mal (no cumple sus expectativas).
- Porque puede poner en peligro su línea de negocio.
- Porque es frustrante usar un SW con errores.

> "La calidad del Software NO se negocia"

¿Qué es una prueba?
> Una prueba es el proceso de ejecutar un SW con el fin de encontrar errores. 

¿Qué **no** es una prueba?
- No es demostrar que no hay errores.
- No es demostrar que el programa funciona.

## Diseño [SOLID]:

- **SRP (Principio de Responsabilidad Única)**. Cada clase debería tener una única responsabilidad.

- **OCP (Principio de Abierto/Cerrado)**. Cada clase debería estar abierta para extensión y cerrada para modificación.

- **LSP (Principio de Sustitución de Liskov)**. Los objetos deberían ser sustituidos por instancias de las clases que implementan sus subtipos sin alterar el programa.

- **ISP (Principio de Segregación de Interfaz)**. Muchas interfaces particulares mejor que una general.

- **DIP (Principio de Inversión de Dependencia)**. No se debe depender de la implementacion.

## Tipos de fallos
- **Defecto (bug)**: Resultado de deficiencia durante el proceso de creación, y que no ha sido descubierto aún. (**todo programa tiene defectos**)
- **Fallo**: aquel defecto que, mediante el uso de pruebas y tests, ha sido hallado
- **Error**: Aquel defecto que no ha sido hallado mediante pruebas y tests, pero que se hace patente durante el funcionamiento del programa.

## Objetivo de las pruebas
- Descubrir los errores cometidos durante la fase de desarrollo, **no demostrar que el programa funciona**.

    - Un buen caso de prueba es aqel que tiene alta probabilidad de mostrar un error no descubierto.
    - La prueba tiene éxito si descubre un error no detectado hasta entonces.

- Garantizar la calidad del código para que además de correcto sea bueno, legible...
    - Normas de programación
    - Patrones de diseño
    - No uso de [antipatrones]

## Tipos de creación de pruebas
- **TDD** (Desarrollo Orientado a Test)
    - Pasos:
        - ROJO: Fallo en test. Esto lleva a crear la clase y los métodos.
        - VERDE: Test pasa.
        - REFACTOR: Eliminar redundancia (limpiar código y tests).

    Los requisitos se convierten en pruebas. Cuando se pasen, se garantiza que el requisito     está cumplido.
    
- **ATDD** (Diseño orientado a test de aceptación)
    - Captura de requisitos para pruebas de aceptación.
    - Normalmente un ciclo ATDD lleva implícito uno o más ciclos TDD.

- **DDD** (Diseño orientado a dominio)
    - Diseño basado en modelo y núcleo del negocio.
    - El foco principal (lo que importa) es el dominio (vista global del producto = negocio)

- **BDD** (Desarrollo orientado a comportamiento)
    - Evolución del TDD
    - Enfocado al lenguaje e programación (nomenclaturas, no programación) y de las interacciones en el proceso de desarrollo.
    - Usar ejemplos o partes de código para descibir el comportamiento de la aplicación.
    - Usar 'should' para liberar responsabilidades y permitir que el comportamiento sea discutido.
    - Utilizar 'mock objects' para simular los módulos no desarrollados.
    
    BDD "utiliza" los nombres de los tests para dar a entender qué hacen o prueban.

## Dobles de prueba
Aislan el componente a probar, sustituyendo las dependencias. Esto nos permite simular el comportamiento.
Existen varios tipos (ordenados por complejidad ascendente):

 - **Dummy**
 
    Satisfacen dependencias no utilizadas con una implementación vacía (los métodos devueven `null`o lanzan una excepción).

 - **Stubs**
    
    Implementación estática de una dependencia; devuelve siempre los mismos valores. Se crean una vez sólo.

 - **Spy**
    
    Stub con capacidad de guardar valores con los que son llamados (espían a quien les llaman). Se pueden consultar posteriormente (núm. de veces que se llama a un método y sus valores). Acoplan el test a la implementación, dando lugar a tests frágiles. 

 - **Mocks**
 
    Objetos preprogramados que simulan el comportamiento de una clase sin implementar la lógica esperaa del objeto. Se puede sobreescribir el comportamiento para variar los valores de retorno.

    Se pueden conseguir de tres maneras:
    - Escibiendo código sencillo de la interfaz.
    - Generación automática de código ('stubbing' en Eclipse).
    - Dinámicamente, sin código asociado al objeto; se genera *on the fly* (*Mockito*, *EasyMock*,...).

 - **Fake**
 
    Implementación simplificada de la lógica. Esto permite simular una implementación más compleja, de manera que se pueden probar varios escenarios.

Hay que utilizar los dobles más sencillos que permitan implementar la funcionalidad, evitando aquellos que condicionen la funcionalidad. Como nota, decir que a todos los dobles se les llama coloquialmente "Mocks".

<a name="def-tipos de pruebas"></a>
# 2. Tipos de Pruebas
Las pruebas se realizan de manera expansiva, de dentro hacia fuera, comenzando con módulos unitarios para acabar con el sistema completo.
- Pruebas unitarias: 
    - Comprueban la lógica, funcionalidad y si es correcta la especificación de cada módulo.
- Pruebas de integración:
    - Tienen en cuenta la agrupación de módulos y el flujo de información entre interfaces.
- Pruebas funcionales: 
    - Validación de historias de usuario y aceptación del usuario.
- Pruebas del sistema:
    - Se integra con el entorno HW y SW.
- Pruebas de aceptación: 
    - Comprueban que el producto se ajusta a los requisitos del usuario.
- Pruebas de regresión: 
    - Todas las pruebas anteriores tras la realización de un cambio (tanto por arreglar un error como por metodologías ágiles).
- Prueba Manual
    - Adaptada a cambios.
    - Hace frente a la complejidad.
    - Detección de errores de un vistazo.
    - Introduce variantes útiles para detectar bugs.
    - Exploración previa para definir casos a utilizar.
- Prueba Automatizada
    -  Limitada a  evaluación/verificación del script.
    -  Complementa a las pruebas manuales.
    -  No sustituye al proceso de prueba.

## Pruebas Unitarias
### Principio FIRST
Una buena prueba unitaria debe ser:
- **Fast (Rápido)**
    - Automatización.
    - Ejecución en el proceso de construcción.
- **Independent/Isolated (Aislada)**
    - Solo probar una cosa (un sólo escenario, un sólo caso de uso).
    - La ejecución del test **no debe condicionar otro** (estado en BD, inicialización previa y reset posterior).
- **Repeatable (Repetible)**
    - Debe poder repetirse todas las veces que haga falta.
    - De forma sencilla.
    - En cualquier entorno.
    - Minimizando dependencias.
- **Self-validating (Auto-evaluable)**
    - El propio test debe evaluar si hay error o no.
    - Sin inspección visual (ni en resultado, ni en trazas).
- **Timely (Oportuno)**
    - Deben escribirse en su momento (antes de escribir código o cuando se detecta un bug).

## Verificación y Validación
- **Verificación** (construido correctamente)
    - Comprueba el funcionamiento del SW, que implemente bien una función específica.
- **Validación** (sistema correcto)
    - Comprueba si los requisitos se has satisfecho de manera correcta.

<a name="def-quality assurance"></a>
# 3. Quality Assurance
**Calidad del Software** es el grado en el que un sistema, componenete o proceso cumple los requisitos espcificados y las necesidades o expectativas del cliente o usuario.

**Aseguramiento de calidad del Software (SQA)** es el conjunto de actividades planificadas y sistemáticas necesarias para aportar confianza en que el producto satisfará los requisitos dados de calidad.

## Trazabilidad
Para poder comprobar la cobertura de los tests se deberían escibir trazas (logs) de cuándo entran y a qué métodos. De esta forma se puede contabilizar las veces que se entra en un método y los caminos que se siguen a través de él.

Otra opción (mejor que la anterior) es usar AOP (programación orientada a objetos).

La mejor opción para estos casos es usar una herramienta para este tipo de tareas (Cobertura, Enma...).

<a name="def-pruebas unitarias"></a>
# 4. Pruebas unitarias JUnit
>Una buena prueba unitaria cumple el Principio **FIRST**
## Pruebas de Java en JUnit
### Estructura de la prueba
- Una clase por cada prueba.
- El nombre de la clase tiene sufijo "Test" (unitaria) o "ITest" (integración).
- Un método para cada caso de prueba:
    - Nombre descriptivo (orientado a BDD).
    - Estructura iniciar-actuar-comprobar.
- Métodos comunes:
    - Preparan escenario de pruebas.
    - Restauran estado inicial.
- Configuración por anotaciones (Java 5 y posteriores).

### Anotaciones de Test
- ***@Test***
    - Representa prueba a ejecutar.
    - Parámetros: 
     - timetout=x (ms). La prueba será válida si se ejecuta en menos tiempo que el especificado.
     - Expected= Exception.class (clase de excepción). La prueba será válida si se lanza la excepción indicada
- ***@Ignore***
    - Indica que no se lance la prueba con un motivo.
- ***@BeforeClass***
    - El método se lanza una sola vez al principio.
- ***@AfterClass***
    - El método se lanza una sola vez al final. Sólo puede haber uno de este tipo.
- ***@Before/@After***
    - El/los método/s se lanza/n al principio/final de cada prueba.

### Funciones de test
- ***Assert***
    - Equivalencia 

    ```java
    assertEquals(Object expected, Object actual)
    //la prueba falla si los parámetros no son iguales
    
    assertNotEquals(Object expected, Object actual)
    //la prueba falla si los parámetros son iguales
    ```
    
    - Identidad
    
    ```java
    assertSame(Object expected, Object actual)
    //la prueba falla si las referencias NO apuntan al mismo objeto
    
    assertNotSame(Object unexpected, Object actual)
    //la prueba falla si las referencias SI apuntan al mismo objeto
    ```
    
    - Nulos
    ```java
    assertNotNull(Object object)
    //la prueba falla si el objeto es nulo
    
    assertNull(Object object)
    //la prueba falla si el objeto no es nulo
    ```

    - Condiciones y expresiones
    
    ```java
    assertFalse(boolean condition)
    //la prueba falla si la condición es true
    
    assertTrue(boolean condition)
    //la prueba falla si la condición es false
    ```
    
    - Mensajes personalizados
    
    Todos los métodos `assert` tienen la opción de especificar el mensaje de error como primer parámetro:
    
    ```java
    assertTrue(String message, boolean condition)
    ```

- ***Fail***
````java
fail(String message)
//hace fallar el test mostrando un mensaje.
```
## Ejercicio 1: KATA TDD StringCalculator
1. Crear un StringCalculator que tenga un método add:

    ```java
    //suma los numeros de la cadena
    int add(String numbers);
    ```

    - a. El método puede recibir 0, 1 o 2 números: "", ó "1" ó "1,2" (por ejemplo).
    - b. Empezar con el caso de prueba más sencillo, cadena vacia, y seguir luego con los demás.
    - c. Hay que pensar que hay que hacer primero el test y luego el código *mínimo* que logra pasar el test. Según se realizan más test, se va completando el código.
    - d. No olvidarse de **refactorizar**.
    
    Solución:
    
        ```java
        public int add(final String numbersToAdd) {
            int sum = 0;
        
            if ("".equals(numbersToAdd)) {
                return sum;
            }
        
            final String[] numberParts = numbersToAdd.split(",");
        
            if (numberParts.length == 1){
                return Integer.valueOf(numberParts[0]);
            }else if (numberParts.length==2){
                return Integer.valueOf(numberParts[0])+ Integer.valueOf(numberParts[0]);
            }
        
            return sum;
        }
        ```

2. Permitir que el método `add` maneje un número indefinido de números: "1,4,5,...,n".

    Basta modificar la siguiente parte del código existente del método:

    ```java
    final String[] numberParts = numbersToAdd.split(",");
    if (numberParts.length == 1){
            return Integer.valueOf(numberParts[0]);
    }else if (numberParts.length==2){
        return Integer.valueOf(numberParts[0])+ Integer.valueOf(numberParts[0]);
    }
    ```
    Por la que sigue:
    ```java
    final String[] numberParts = numbersToAdd.split(",");
    for (final String part : numberParts) {
        sum += Integer.valueOf(part);
    }
    ```
    
## Hamcrest
Es una librería de utilizar que simplifica las aserciones. Se basa en una serie de Matchers que permiten escribir las aserciones de forma más simple.

### Hamcrest Matchers
- anything
- describedAs
- is
- allOf
- anyOf
- not
- equalTo
- hasToString
- instanceOf
- notNullValue, nullValue
- sameInstance
- hasProperty

## Ejercicio 2: Uso de matchers de Hamcrest
1. Modifica el código para usar los matchers de Hamcrest

    Basta añadir los `import` y cambiar los `assert` por los matchers:
    ```java
    //cambiar las sentencias del tipo
    assertEquals(Object expected, Object actual);
    //por
    assertThat(Object actual, is(equalTo(Object expedted)));
    ```

## Mocking
Es una técnica utilizada para la simulación de objetos cumpliendo el mismo contrato que los objetos reales.<br>
Se utiliza para aislar el componente que queremos probar de sus dependencias (Se sustituyen las dependencias de un objeto por otras)

### Origen
Surgió porque se necesitaba una forma de aislar los tests de las dependencias con otros objetos, poder desarrollar las funcionalidades a pesar de no tener implementado el objeto real y cumplir con el principio FIRST en los test unitarios.

### Características de los tipos
Ya hemos visto los tipos de dobles que existen, veamos sus características:

- **Dummy**
    - Son sencillos de crear: la implementación está vacía y se pueden generar directamente con el IDE.
    - Se pueden reutilizar.
    - Hay que mantenerlos: la implementación es completa y esta sujeto a cambios.

- **Stubs**
    - 

- **Spy**
    - 

- **Mock**
    - 

- **Fake**
    - 

### Uso de Dobles

Hay que utilizar el doble más sencillo, valorando la reutilización (puede acolparse a la prueba) y tener en cuenta que hay que mantenerlo.

- **Frameworks de Mocks**
    Nos aportan agilidad, facilidad y reducción del tiempo de desarrollo y del núm. de clases.
    - EasyMock
    - JMock
    - Mockito
    - JMockit

- **Tipos de Framework**
    - *Expect-run-verify* (EasyMock y JMOck) <br>
        Los test verifican innecesariamente ciertas comprobaciones que no son importantes en el resultado. Esto aumenta la fragilidad de los tests (los cambios requieren iteraciones adicionales).

    - *No expect-run-verify* (Mockito) <br>
        Los test verifican lo que importa, separando la parte de entrenamiento/simulacion de la verificación

<a name="def-mockito"></a>
# 5. Mockito
Librería Java para la creación de Mocks.
## Características
- Se pueden crear mocks de interfaces y clases concretas.
- Verificación de invocaciones (número, orden, al menos una vez, etc).
- Stack trace limpio, ya que los errores ocurren en los `asserts` que se hagan (y no dentro del método bajo prueba).
- API clara para crear stubs y verificaciones.

## Estructura de un test con Mockito
Cada método tiene la siguiente estructura:
1. Creación del Mock dinámicamente.
2. Preparación de respuestas (opcional).
3. Ejecución del test.
4. Verificación de las invocaciones (opcional; todas o sólo algunas).

El mock actúa como 'dummy' para los métodos que no han ejecutado el paso 2.

## Creación de Mocks

- Programática (normalmente se usa esta forma)

    ```java
    mock(Class class)
    ```

- Anotaciones
    
    ```java
    @Mock
    //creación con 
    MockitoAnnotations.initMocks(this)
    ```

- Runners (inicializa automáticamente)
    
    ```java
    @RunWith(MockitoJUnitRunner.class)

    @Mock Class mock
    ```

## Mock stubbing
Se prepara la respuesta del mock ante ciertas invocaciones (permite 'ensayar' las llamadas que el doble debería recibir). <br>
Por defecto, para los métodos que devuelven algo, el mock devuelve null, colección vacía o el valor primitivo adecuado. Este valor se puede sobreescribir.

```java
//se elige método, **parámetro** y retorno
when(mock.método(param)).thenReturn(retorno)
when(mock.método(param)).thenThrow(excepcion)

//excepciones con métodos void
dothrow(excepcion).when(mock).metodo()
```

Si se hace de forma iterativa se controla lo que se hace tras llamadas sucesivas al método mockeado (cada vez que se llama se hace una cosa, a partir de la última se repite):

```java
when(mock.método(param)).thenThrow(excepcion).thenReturn(retorno)

//la primera vez que se llama se lanza la excepción; segunda y sucesivas devuelven lo indicado
```

## Verificación
Después de ejecutar la prueba, se puede verificar con el mock que se han invocado los métodos esperado (acopla el test a la implementación).

```java
verify(mock).metodo(parametro)

//numero de invocaciones
verify(mock, times(numeroLlamadas)).metodo(parametro)
verify(mock, atLeast/mostOnce/most(numeroLlamadas)).metodo(parametro)
```

## Interacciones inesperadas
Para comprobar que otros mock no han sido llamados se utiliza

```java
verifyZeroInteractions(mock)

//verifica que no hubo mas interacciones con el mock de las que se han verificado ya
veryfyNoMoreInteractions(mock)
```
El **abuso** de estas funciones puede dar lugar a test frágiles.

## Argument Matchers
Nos da mayor flexibilidad a la hora de entrenar un stub o en la verificación de un mock.

```java
anyInt()
anyString()
eq("cadena")
```
Hay que ser consistentes en el uso: si se usa un matcher en un argumento, el resto también deberían serlo


## Orden de llamada

Si se quiere comprobar el orden en el que se llaman los métodos se usa `inOrder`

<a name="def-easy mock"></a>
# 6. EasyMock
Es la otra gran alternativa a Mockito. La diferencia reside en la nomenclatura de métodos y la sencillez de su API (Mockito es más simple).

## Ejercicio 3: Aplicar principio de responabilidad única
1. Modifica el código de la clase `StringCalculator` para que la separación de cadenas la haga una clase `StringSplitter`

    - a. Como siempre en TDD, se empieza por los tests.
    - b. Una vez especificados los tests, se crea la clase que cumple las funcionalidades mínimas pedidas:

    ```java
    public class StringSplitter {

        public String[] split(final String stringToSplit, final String separator) {
            if (stringToSplit == null || separator == null) {
                return null;
            } else if ("".equals(stringToSplit)) {
                return new String[] {};
            } else if ("".equals(separator)) {
                return new String[] { stringToSplit };
            }

            final String[] stringParts = stringToSplit.split(separator);
            return stringParts;
        }
    }

    ```

    - c. Ya sólo queda añadir este componente a la clase `StringCalculator`:

    ```java
    //se crea el constructor con el parámetro pedido y se cambia la linea de llamada a *split*
    private final StringSplitter stringSplitter;

    public StringCalculator(final StringSplitter stringSplitter) {
        this.stringSplitter = stringSplitter;
    }

    //metodo *add*
    final String[] numberParts = stringSplitter.split(numbersToAdd, ",");

    ```

<a name="def-cobertura"></a>
# 7. Cobertura
- Herramienta Java que calcula el procentaje de código accedido por tests, además de la complejidad ciclomática de las clases a probar. Se puede ejecutar desde Ant, línea de comandos o dentro del ciclo de vida de Maven.
- Instrumentaliza el código.
- Genera informes en HTML o XML.

Se puede configurar Maven para que la build falle si no se alcanza una cierta cobertura

<a name="def-aproximacion progresiva"></a>
# 8. Aproximación Progresiva
Cuando se recibe un código heredado sin tests *¡Que no cunda el pánico! Piensa en TDD*.
- No pretender hacer las pruebas de golpe.
- Hacer las pruebas de nuevos desarrollos, cuando aparece un bug o cuando se modifica una funcionalidad.
- En vez de fijar un % de cobertura mínimo, fijar un ritmo de incremento, tanto de la cobertura como de número de pruebas.

<a name="def-medida calidad"></a>
# 9. Medida de la Calidad del Código

## PMD
Verifica reglas de codificación buscando en el código fuente posibles errores:
- Nombres de variables muy cortos.
- Concatenación de strings en vez de uso de StringBuffer.
- Bloques de código sin llaves.
- Código repetido.
- ...

Contiene un cojunto de reglas predefinidas en ficheros XML. Estas reglas se pueden cambiar, escribir nuevas, personalizarlas...

## CheckStyle
Permite escribir código java "estándar". A partir de la versión 3 realiza otras tareas:
- Problemas en diseño de clases.
- Código duplicado.
- Javadoc.
- ...

## FindBugs
Alternativa OpenSource a PMD. Opera en bytecodes y no en código fuente. Además posee un pequeño análisis de dataflow (permite detectar referencias a null).

## SonarQube
Plataforma de código abierto que ofrece muchas features para la mejora de la calidad del código.
- Analiza el código fuente lanzando Squid, CPD y FindBugs.
- Testing unitario.
- ...

Provee un 'DashBoard' donde se encuentran todos los proyectos que se despliegan en la herramienta, mostrando:
- Líneas de código.
- Deuda técnica.
- Cobertura.
- Código duplicado.
- Fecha de construcción.

Las reglas se pueden personalizar con XPath, configurándolas para tener distintos análisis del código.

<a name="def-mas alla"></a>
# 10. Más allá

## JUnit - Spring: Pruebas de integración
- Soporte a pruebas en spring-test.jar
- Pruebas de integración (varios componentes trabajando juntos).
- Permite usar un contexto de aplicación específico (sólo unos cuantos componentes). 
- Para usar JUnit se lanza un [runner especial].
- La SUT debe ser un `@Resource`.

<a name="def-runner"></a>
### SpringJUnit4TestRunner
- Carga el ApplicationContext y lo mantiene entre distintos tests (a menos que se le indique que no).
- Prepara e inyecta las dependencias en el propio test.
- ***@ContextConfiguration**
    - Permite especificar la forma de cargar el contexto de aplicación de Spring.

## Ejercicio 4: Test de Integración
1. Crea una clase `ContactBean` y pruébala usando el contexto de Spring
    a. Crea el test que pruebe el caso, con el contexto y la inyección del objeto

    ```java
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration("/applicationContext-test.xml")
    public class ContactBeanIT {

        @Resource
        private ContactBean contactBeanSUT;

        @Test
        public void shouldReturnCorrectContactBeanName() {
            assertThat(contactBeanSUT.getName(), is(equalTo("Pepe")));
        }

        @Test
        public void shouldReturnCorrectContactBeanSurname() {
            assertThat(contactBeanSUT.getSurname(), is(equalTo("Ruiz")));
            assertThat(contactBeanSUT.getPhoneString(), is(equalTo("666-111-112")));
        }

        @Test
        public void shouldReturnCorrectContactBeanPhoneString() {
            assertThat(contactBeanSUT.getPhoneString(), is(equalTo("666-111-112")));
        }

    }
    ```

    b. Crea el contexto de Spring con la definición del Bean:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- constructor-arg nos permite volver *final* los atributos en la clase que modeliza el bean, así no creamos los setters -->
        <bean name="contact" class="com.autentia.cursos.tdd.ejercicios.ContactBean">
            <constructor-arg name="name" value="Pepe"/>
            <constructor-arg name="surname" value="Ruiz"/>
            <constructor-arg name="phoneString" value="666-111-112"/>
        </bean>
    </beans>
    ```

    c. Crea la clase `ContactBean`:

    ```java
    public class ContactBean {

        private final String name;

        private final String surname;

        private final String phoneString;

        public ContactBean(final String name, final String surname, final String phoneString) {
            this.name = name;
            this.surname = surname;
            this.phoneString = phoneString;
        }

        public String getName() {
            return name;
        }

        public String getSurname() {
            return surname;
        }

        public String getPhoneString() {
            return phoneString;
        }

    }
    ```

<a name="def-pruebas funcionales"></a>
# 11. Pruebas funcionales

## Selenium
### Componentes

- **Selenium Core:** Motor de ejecución.
- **Selenium IDE:** Graba los test en HTML.
- **Selenium Server:** Ejecución remota de los tests para varios lenguajes.

### WebDriver (usado con Selenium 2)
- **Driver**: componente que simula un navegador.
    ```java
    Driver driver = new ChromeDriver()
    Driver driver = new FirefoxDriver()
    ```
    - Se trae una página web con el método `get(String url)`.
    - Componentes web a través del método `findElement(String locator)` o `findElements(String locator)` junto con `By`.
        ```java
        List<WebElement> posts = driver.finElement(By.cssSelector("ul.posts li"));
        WebElement post = posts.get(0);
        ```
    - Acciones sobre la página con métodos de 'botón': `click`,`submit`, ...

## JMeter
Para probar aplicaciones Web; evolucionó con nuevas funciones. Permite realizar pruebas de rendimiento y esfuerzo sobre una aplicación.

### Componentes
- ThreadGroup (operaciones de usuario).
- Samplers (peticiones que se realizan en el sistema: HTTP, FTP, JDBC,...).
- Logic Controllers (simulan secuencias lógicas: decisiones, bucles...).
- Timers (pausa entre peticiones, simula comportamiento de usuario).
- Listeners (controlar tiempo de respuesta, permiten mostrar gráficos).


## SoapUI
Herramienta para automatizar invocación y simulación de servicios para realizar pruebas de carga y comportamiento.

- Si ya existe el servicio:
    - Crear petición a partir del WSDL
    - Modificar la petición.
    - Establecer aserciones.
    - Guardar el test en batería de pruebas.


---

<a name="def-anexos"></a>
# **Anexos**

# Anexo A: La Biblia del TDD
Aquí encontraremos las cosas más importantes del curso, las claves del TDD.

### Diseño [SOLID]:

- **SRP (Principio de Responsabilidad Única)**. 

- **OCP (Principio de Abierto/Cerrado)**. 

- **LSP (Principio de Sustitución de Liskov)**. 

- **ISP (Principio de Segregación de Interfaz)**. 

- **DIP (Principio de Inversión de Dependencia)**.

> ** Una prueba es el proceso de ejecutar un SW con el fin de encontrar errores. **

¿No hay tiempo para hacer pruebas? **FALSO**<br>
El tiempo dedicado a pruebas se devuelve con creces.

### Conceptos Clave
* **Defecto (bug)**: Resultado de deficiencia durante el proceso de creación, y que no ha sido descubierto aún. (**todo programa tiene defectos**)
* **Fallo**: aquel defecto que, mediante el uso de pruebas y tests, ha sido hallado
* **Error**: Aquel defecto que no ha sido hallado mediante pruebas y tests, pero que se hace patente durante el funcionamiento del programa.

### Tipos de desarrollo de pruebas
- **TDD** (Desarrollo Orientado a Test)
- **ATDD** (Diseño orientado a test de aceptación)
- **DDD** (Diseño orientado a dominio)
- **BDD** (Desarrollo orientado a comportamiento)

### Dobles de prueba
 - Dummy
 - Stubs
 - Spy
 - Mocks
 - Fake

###Una buena prueba unitaria debe ser **FIRST**
- Fast
- Independent
- Repeatable
- Self-validating
- Timely

# Anexo B: Frases clave

>Una buena prueba unitaria debe ser **FIRST**

> "La calidad del Software **NO** se negocia".

> Una prueba es el proceso de ejecutar un SW con el fin de encontrar errores. 

> Un DAO no se puede probar de forma unitaria. Siempre necesitas una BD contra la que lanzarlo.

> Mockito **no** crea Mocks

> Programa siempre como si el tipo que acabase manteniendo tu código fuera un violento psicópata asesino que sabe donde vives

---


<!--- link usados  -->
[runner especial]:#def-runner
[SOLID]:https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)
[antipatrones]:http://c2.com/cgi/wiki?AntiPatternsCatalog
