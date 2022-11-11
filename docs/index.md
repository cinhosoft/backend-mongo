# Crear APIREST con NetBeans SpringBoot y MONGODB

## 1. Crear la Proyecto:

Vamos a File New Project…

![](imagenes\2022-11-06 15_46_40-Window.png)

Luego Seleccionar la Categoria: Java with Maven y en Project: Spring Boot Inizializr project

![](imagenes\paso2.png)

Digitamos el Nombre de nuestro grupo de paquetes, el Artifact, el Empaquetado, la descripción, el lenguaje Java y la version:

![](imagenes\paso3.png)

Agregamos las dependencias:

![](imagenes\dependencias.png)

Buscamos la dependencia de MONGODB:

![](imagenes\paso4.png)

Agregamos el nombre y ubicación del proyecto:

![](imagenes\paso5.png)

## 2. Conexion a la base de datos en MONGO Atlas

Se debe iniciar sesion en [account.mongodb.com](account.mongodb.com)

![](imagenes\paso6.png)

Si no se tiene cuente se pueden registrar en [https://account.mongodb.com/account/register](https://account.mongodb.com/account/register):

![](imagenes\paso7.png)

Una vez se inicia sesion, se debe crear el cluster, nos dan uno gratis, con ciertas limitaciones para pruebas:

![](imagenes\paso8.png)

Una vez terminamos de crear el cluster, debemos crear un usuario:

![](imagenes\paso9.png)

Tambien es importante configurar el modo de conexion, en nuestro caso vamos a usar Cloud:

![](imagenes\paso10.png)

Debemos configurar las IP las cuales queremos que se puedan conectar, podemos dar click en Add My Current IP Address, para la IP desde la que estamos saliendo a Internet Actualmente, Si quieres permitir conexiones de cualquier aplicacion puedes agregar 0.0.0.0:

![](imagenes\paso11.png)

## 3. Crear la Base de Datos en Mongo Nube:

Vamos a dar click en Browse Collections:

![](imagenes\paso12.png)

Luego debemos dar click en Create Database, digitar el nombre de la base de datos y la primera coleccion, que en este ejemplo la llamamos anotaciones:

![](imagenes\paso13.png)

Una vez terminamos de crear la base de datos debemos tener algo como esto:

![](imagenes\paso14.png)

Se debe generar la cadena de conexión a la base de datos, para eso vamos a dar click en Database y luego Connect:

![](imagenes\paso15.png)

Seleccionamos la opcion Connect your application:

![](imagenes\paso16.png)

Vamos a seleccionar Java y la ultima Version Estable de SpringBoot, para que se genere la cadena de conexion de ejemplo la cual debemos compiar:

![](imagenes\paso17.png)

La cadena de conexion la debemos llevar al properties quedando asi:

```Java
spring.data.mongodb.uri=mongodb+srv://marlongalvis30:CONTRASEÑA@montomisiontic.irlknlv.mongodb.net/BitacoraMongo?retryWrites=true&w=majority
```
![](imagenes\paso18.png)

## 4. Crear el Modelo

Debemos crear el paquete models y despues agregamos una clase de java:

![](imagenes\paso19.png)

Creamos la clase Anotacion:

![](imagenes\paso20.png)

Una vez creado vamos agregar los campos de la clase, no es necesario agregar la anotacion @Entity solo vamos a agregar la anotacion @Id para especificar la llave primaria de la coleccion que llevara el mismo nombre de la clase "anotacion".

Los Campos Quedarían Así:
```Java
@Id
    private String id;
    private String anotacion;
    private String usuario;
    private LocalDate fecha_actualizacion;
```
Luego procedemos a crear el contructor y los getter and setter:

Damos Click derecho y luego Insert Code...

![](imagenes\paso21.png)

Luego Click en Constructor: 
![](imagenes\paso22.png)

Vamos a dar click en Select All para sobrecargar el constructor con todos los campos y luego click en Generate:

![](imagenes\paso23.png)

Repetirmos el paso anterior click derecho y luego Inser Code... para llegar al punto donde aparece el menu, para seleccionar Getter And Setter:

![](imagenes\paso24.png)

El modelo de la Clase Antotaciones completo debe quedar así:

```Java
package co.edu.unab.api.models;

import java.time.LocalDate;
import org.springframework.data.annotation.Id;

/**
 *
 * @author Marlon
 */
public class Anotacion {
    
    @Id
    private String id;
    private String anotacion;
    private String usuario;
    private LocalDate fecha_actualizacion;

    public Anotacion(String id, String anotacion, String usuario, LocalDate fecha_actualizacion) {
        this.id = id;
        this.anotacion = anotacion;
        this.usuario = usuario;
        this.fecha_actualizacion = fecha_actualizacion;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getAnotacion() {
        return anotacion;
    }

    public void setAnotacion(String anotacion) {
        this.anotacion = anotacion;
    }

    public String getUsuario() {
        return usuario;
    }

    public void setUsuario(String usuario) {
        this.usuario = usuario;
    }

    public LocalDate getFecha_actualizacion() {
        return fecha_actualizacion;
    }

    public void setFecha_actualizacion(LocalDate fecha_actualizacion) {
        this.fecha_actualizacion = fecha_actualizacion;
    }
    
    
    
    
}
```

## 5. Crear el Repositorio

Vamos a crear el paquete repositories, luego damos click derecho sobre el paquete para crear el Repositorio AnotacionRepository, seleccionamos Repository Interface...

![](imagenes\paso25.png)

Digitamos el nombre de la Clase AnotacionRepository, luego digitamos en el campo Entity Class el nombre del modelo Anotacion y el campo Id Class el tipo de dato del Identificador del Modelo en este ejemplo String. 

![](imagenes\paso26.png)

En este caso en el paso anterior no seleccionamos una Base Interface sino que dejamos la que estaba seleccionada quedando el repositorio así:

![](imagenes\paso27.png)

En este caso para MongoDB no sirve ninguna de las opciones que da el plugin, por lo que vamos a editar el extends para que el repositorio herede de MongoRepository, adicionalmente agregamos el import del Modelo Anotacion, quedando el codigo de AnotacionRepository asi:

```Java
package co.edu.unab.api.repositories;

import co.edu.unab.api.models.Anotacion;
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.data.repository.CrudRepository;

/**
 *
 * @author Marlon
 */
public interface AnotacionRepository extends MongoRepository<Anotacion, String> {
    
}
```
## 5. Crear la Interface para el Servicio

Vamos agregar el paquete service y despues presionamos click derecho sobre el paquete New Java Interface

![](imagenes\paso28.png)

Digitamos el nombre de la Interface del Servico IAnotacionService:

![](imagenes\paso29.png)

Agregamos los metodos que vamos a utilizar en el Servicio quedando la Clsae IAnotacionService Así:

```Java
package co.edu.unab.api.services;

import co.edu.unab.api.models.Anotacion; 
import java.util.ArrayList;
import java.util.Optional;

/**
 *
 * @author Marlon
 */
public interface IAnotacionService {
    public ArrayList<Anotacion> listarTodo();
    public Anotacion guardar(Anotacion anotacion);
    public boolean eliminar(String id);
    public Optional<Anotacion> obtenerPorID(String id);
    public ArrayList<Anotacion> obtenerPorUsuario(String usuario);
    
}
```

## 6. Implementamos la Interface Services

Debemos agregar dentro del paquete services el paquete Impl, damos click derecho sobre el paquete services y New Java Package...

![](imagenes/paso30.png)

Digitamos el nombre del Paquete el cual vamos a llamar impls:

![](imagenes/paso31.png)

Una vez creado el paquete debemos dar click derecho sobre el paquete impls y agregamos una nueva Service Class:

![](imagenes/paso32.png)

Digitamos el nombre de la Clase AnotacionService:

![](imagenes/paso33.png)

Una vez creada la clase vamos a implementar la interface con el comando `implements IAnotacionService` importamos la clase IAnotacionService e implementamos todos los metodos abstract.

Para conectar el repositorio con el servicio vamos a inyecta el repositorio con la anotacion `Autowired` y declaramos la variable de Tipo AnotacionRepository quedando el codigo así:

```Java
    @Autowired
    ClienteRepository clienteRepository;
```

Una vez definimos la variable con la que vamos a realizar las transacciones con el repositorio de Mongo, procedemos a programar cada uno de los metodos:

El metodo listarTodo quedaría Así:

```Java
    @Override
    public ArrayList<Anotacion> listarTodo() {
        return (ArrayList<Anotacion>) anotacionRepository.findAll();
    }
```

El metodo guardar quedaría Así:

```Java
    @Override
    public Anotacion guardar(Anotacion anotacion) {
        anotacion.setUsuario(anotacion.getUsuario().toLowerCase()); //Opcional, convertir la cadena a minúscula para facilitar buscar por nombre , esto normalmente se hace desde el frontend 
        return anotacionRepository.save(anotacion);
    }
```

El metodo eliminar quedaría Así:

```Java
    @Override
    public boolean eliminar(String id) {
        if (anotacionRepository.existsById(id)){
            anotacionRepository.deleteById(id);
            return true;
        }else{
            return false;
        }    
    }
```

el metodo obtenerPorID quedaría Así:

```Java
    @Override
    public Optional<Anotacion> obtenerPorID(String id) {
         return anotacionRepository.findById(id);
    }
```

para el metodo obtenerPorUsuario, debemos agregar una consulta en el repositorio AnotacionRepository para que se ejecute la busqueda por el campo usuario, que en este ejemplo seria el nombre del usuario, debemos modificar el archivo AnotacionRepository y agregar dentro de la Clase el siguiente codigo:

```Java
    @Query("{usuario:'?0'}")//El ?0 marcador de posición le permite sustituir el valor de los argumentos del método en la cadena de consulta JSON.
    ArrayList<Anotacion> BuscarPorUsuario(String usuario);
```

Quedando el AnotacionRepository Así:

![](imagenes/paso34.png)

Entonces en el AnotacionService el metodo obtenerPorUsuario quedaría Así:

```Java
    @Override
    public ArrayList<Anotacion> obtenerPorUsuario(String usuario) {
        return anotacionRepository.BuscarPorUsuario(usuario);
    }
```
## 6. Crear el Controlador

Vamos a crear el controlador, nos ubicamos en el paquete principal y creamos un nuevo Java Package, de nombre controllers y despues nuevamente damos click derecho esta vez sobre el paquete controllers y agregamos un nuevo archivo REST Controller Class:

![](imagenes/paso35.png)

Le digitamos el nombre AntotacionRestController, checkeamos el Generate CRUD methods y damos click en finalizar:

![](imagenes/paso36.png)

Modificamos la /url por /anotacion y los Object por la Clase Modelo Anotacion, cuando se cambie el Object por la clase Anotacion nos pedira que importemos Así:

![](imagenes/paso37.png)

El primer paso es inyectar nuestro servicio y lo vamos hacer con la anotacion `@Autowirded` quedando la declaración así:

```Java
    @Autowired
    private IAnotacionService anotacionService;
```

Ahora vamos a codificar el metodo List para mostrar todas las anotaciones y quedaría Así:

```Java
    @GetMapping()
    public List<Anotacion> list() {
        return  anotacionService.listarTodo();
    }
```

El Siguiente metodo es el get el cual lo vamos autilziar para buscar por Id y quedaría Asi:

```Java
    @GetMapping("/{id}")
    public Object get(@PathVariable String id) {
        return this.anotacionService.obtenerPorID(id);
    }
```

El Siguiente metodo que vamos a editar es el metodo post el cual se va a utilizar para guardar, agreamos al metodo un segundo campo que sera utilizado para validar el request:

`BindingResult result`

El metodo quedaría Así:

```Java
    @PostMapping
    public ResponseEntity<?> post(@RequestBody Anotacion input, BindingResult result) {
        Anotacion nuevoDato = null;
        Map<String, Object> response = new HashMap<>();
        if (result.hasErrors()) {
            List<String> errors = result.getFieldErrors()
                    .stream()
                    .map(err -> "El campo '" + err.getField() + "' " + err.getDefaultMessage())
                    .collect(Collectors.toList());

            response.put("errors", errors);
            return new ResponseEntity<Map<String, Object>>(response, HttpStatus.BAD_REQUEST);
        }
    try {
            nuevoDato = anotacionService.guardar(input);
        } catch (DataAccessException e) {
            response.put("mensaje", "Error al realizar el insert en la base de datos");
            response.put("error", e.getMessage().concat(": ").concat(e.getMostSpecificCause().getMessage()));
            return new ResponseEntity<Map<String, Object>>(response, HttpStatus.INTERNAL_SERVER_ERROR);
        }

        response.put("mensaje", "El dato ha sido actualizado con éxito!");
        response.put("anotacion", nuevoDato);
        return new ResponseEntity<Map<String, Object>>(response, HttpStatus.CREATED);
    }
```

Por ultimo el metodo eliminar el cual quedaria Así:

```Java
    @DeleteMapping("/{id}")
    public ResponseEntity<?> delete(@PathVariable String id) {
       Map<String, Object> response = new HashMap<>();
       boolean resultadoEliminar=this.anotacionService.eliminar(id);
        if (resultadoEliminar){
            response.put("mensaje", "Se eliminó el usuario con id: "+id);
           
        }else{
             response.put("mensaje", "No se pudo eliminar el usuario con el id: "+id);
             return new ResponseEntity<Map<String, Object>>(response, HttpStatus.INTERNAL_SERVER_ERROR);
        }
        return new ResponseEntity<Map<String, Object>>(response, HttpStatus.OK);
    }
```

