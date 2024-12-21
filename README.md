# Rol de programador y tester
## Tecnologías Empleadas: 
1.	Lenguaje de Programación: Java (JDK 21) 
2.	Frameworks: Spring Framework, Spring Boot, Spring Data JPA 
3.	Diseño visual: React
4.	Base de Datos: PostgreSQL 12 con extensión GIS 
5.	Herramientas de Desarrollo: IntelliJ IDEA, Visual Studio Code, Docker 
6.	Control de Versiones: Git, GitHub 
7.	Gestión de Migraciones: Flyway 
8.	Calidad de Código: SonarLint 
9.	Manejo de Datos: Apache POI, SQL, PL-SQL  

## Estándar de codificación:
1.	Todos los métodos y variables dentro de clases se encuentran escritos en español con la demarcación: del verbo acción + área afectada.
2.	Las clases de modelados de datos quedan exceptas de responsabilidades, solamente se ocupan de almacenar la información
3.	Tipografía camelCase para los métodos y variables.
4.	 No utilizar break para frenar ciclos, mejor utilizar return o lanzar excepciones
5.	La captura de excepciones se hace en las clases controller y no en los métodos que esta invoca
6.	Incluyo comentarios breves solo cuando sea necesario, especialmente para explicar lógica compleja. 
7.	Las clases comienzan con mayúscula y siguen PascalCase (por ejemplo, UsuarioController)
8.	Se lanzan excepciones específicas cuando es necesario.
9.	Los mensajes de error devueltos son claros, pero sin exponer información sensible
10.	Las validaciones se realizan a través de notaciones y un disparador que confirma si las validaciones son correctas.
11. La conexión a la base de datos se realiza a través de un ORM que se encarga de la conexión a las base de datos y de los CRUD del sistema.
12. Las condifiguraciones son llevadas por archivos .propiertes o .yml 
13. Los datos como las direcciones o claves son parte de las variables del sistema por lo tanto no son públicas en GitHub o en el código directamente.

## Fragmentos de código:
    @GetMapping("/")
    @Operation(summary = "Listado de municipios",
    description = "Permite obtener el listado de todos los municipios del sistema",security = { @SecurityRequirement(name = "bearer-key") })
    @PreAuthorize(value = "hasAnyRole('Super Administrador','Administrador','Gestor')")
    public ResponseEntity<?> obtenerMunicipios(HttpServletRequest request) {
        String actividad = "Listar todos todos los municipios del sistema";
        TokenDto tokenDto = TokenUtils.getTokenDto(request);
        try {
            List<MunicipioDto> municipioDtos = municipioServices.listadoMunicipios().stream()
                    .map(MunicipioDto::new)
                    .sorted(Comparator.comparing(MunicipioDto::getUuid))
                    .toList();
            registroUtils.insertarRegistro(mapper.convertValue(tokenService.tokenExists(tokenDto).getBody(), UsuarioDto.class).getUsername(),actividad, IpUtils.hostIpV4Http(request),"Aceptado",null);
            return ResponseEntity.ok(municipioDtos);
        }catch (Exception e){
            registroUtils.insertarRegistro(mapper.convertValue(tokenService.tokenExists(tokenDto).getBody(), UsuarioDto.class).getUsername(),actividad,IpUtils.hostIpV4Http(request),"Rechazado",e.getMessage());
            return ResponseEntity.badRequest().body("No se ha podido obtener el listado de municipios, compruebe su conexiòn a la base de datos o contacto con el servicio tècnico");
        }
    }

    @GetMapping("/")
    public ResponseEntity<RespuestaEstandar> listarPaises(){
        try{
            return ResponseEntity.status(HttpStatus.OK).body(new RespuestaEstandar(paisService.buscarPaises()));
        }catch (Exception e){
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(new RespuestaEstandar("No se puede obtener el listado de países. Contacte con su servicio de atención"));
        }
    }

En estos fragmentos de código podemos encontrar la aplicación del estandar de codificación empleado.

Configuración de uno de los microservicios:
<pre>
spring:
  banner:
    location: banner/banner.txt
  application:
    name: Pais
  datasource:
    url: jdbc:postgresql:${GestionApicola}
    username: ${DB_USER}
    password: ${DB_PASSWORD}
    driver-class-name: org.postgresql.Driver
    hikari:
      connection-timeout: 60000
    jpa:
      format-sql: true
      hibernate:
        ddl-auto: update
      properties:
        hibernate:
          format_sql: true
        javax:
          persistence:
            query:
              timeout: 60000
      database: postgresql
      show-sql: true
  cloud:
    config:
      enabled: false
      import-check:
        enabled: false
server:
  port: 8086
  error:
    include-stacktrace: never
  address: localhost
  tomcat:
    connection-timeout: 40000
    max-wait: 100000
springdoc:
  api-docs:
    path: /api-docs
  swagger-ui:
    path: /
</pre>

## Gestión de la configuración
La gestión de la configuración se lleva a cabo a través de Git y GitHub como repositorio empleado.

### Reglas para el control de versiones:
1. Salvar cada vez que se desarrolla un cambio importante en el proyecto o previo a cualquier interrupción importante que pueda ocurrir
2. Nombres claros en cada commit que permita saber que cambios relevantes fueron realizados
3. Cada rama representa a un microservicio y dentro solamente se trabaja en base a dicho microservicio
4. Repositorio remoto en oculto para garantizar la privacidad del código y los datos empleados.

[Imagen de la utilización de git y las ramas](imagen.jpg)

## Pruebas: 
### Prueba de aceptación:
Verifica si todo el sistema funciona según lo previsto. Se llevo a cabo una prueba de aceptación de contratos, para comprobar si el software cumplía con todos los requisitos planteados en las distintas fases de entregas orientadas para el desarrollo de software. Llegando a la conclusión del incumpliendo de los objetivos para el desarrollo, pero determinando puntos donde mejorar y apresurando el proceso de desarrollo de las siguientes fases, para conseguir cumplir con todas las tareas.

Fecha: 9/9/2027
Tareas cumplidas de esta fase
•	Desarrollo de una API que migró todo el sistema previo desarrollado para ordenador
•	Estructuración con un esquema de microservicios
•	API Gateway para la redirección de las conexiones
•	Módulos necesarios para la gestión de los datos
•	Implementación de la seguridad a través de JSON Web Token
•	Importar los datos del Excel a una base de datos	Tareas incumplidas para esta fase
•	Desarrollo de una interfaz visual atractiva
Tareas para las próximas fases:
•	Trabajo con el servicio para la geolocalización de los valores
•	Representación de los datos en un mapa

### Pruebas de caja negra:
Las pruebas de caja negra consisten en probar un sistema o programa informático sin tener conocimiento previo de su funcionamiento interno. Esto no sólo se refiere a no conocer el código fuente en sí, sino que implica no haber visto ninguna de las documentaciones de diseño que rodean al software. Los probadores se limitan a dar entrada y recibir salida como lo haría un usuario final. Aunque se trata de una simple definición de prueba de caja negra, establece el sistema general.

#### Prueba de creación del modelado de la base de datos con errores:
Nombre de la prueba: Creación del modelo la base de datos a través del ORM
Módulo(s): Todos
Precondiciones esperadas:Configuradas las variables de entorno del sistema
Precondiciones reales: Las variables de entorno no están configuradas
Datos de entrada: No necesarios
Resultado esperado: Error de compilación en el sistema por falta de configuraciones
Resultado real: Error de compilación en el sistema por falta de configuraciones

#### Prueba de creación del modelado de la base de datos sin errores:
Nombre de la prueba: Creación del modelo la base de datos a través del ORM
Módulo(s): Todos
Precondiciones esperadas: Configuradas las variables de entorno del sistema
Precondiciones reales: Configuradas las variables de entorno del sistema
Datos de entrada: No necesarios
Resultado esperado: Proyecto compilado con éxito y la base de datos se ha creado con éxito
Resultado real: Proyecto compilado con éxito y la base de datos se ha creado con éxito

#### Prueba de la migración de los datos de la base de datos con Flyway:
Nombre de la prueba: Migración de los datos de la base de datos con Flyway
Módulo(s): Gestión y Login
Precondiciones esperadas: Configuración apropiada y una base de datos con extensión .sql y con el formato correcto
Precondiciones reales: Configuración apropiada y una base de datos con extensión .sql y con el formato correcto
Datos de entrada: No necesarios
Resultado esperado: Proyecto compilado con éxito y la base de datos ha cargado la salva de sus datos anteriores
Resultado real: Proyecto compilado con éxito y la base de datos ha cargado la salva de sus datos anteriores

#### Prueba de insertar entidad sin una autenticación correcta:
Nombre de la prueba: Insertar una entidad
Módulo(s): Gestión
Precondiciones esperadas: Usuario autentificado en el sistema
Precondiciones reales: Usuario no autentificado
Datos de entrada: Entidad con todos sus campos obligatorios correctos
Resultado esperado: Error de autenticación y autorización 
Resultado real: Error de autenticación y autorización 

#### Prueba de insertar entidad con datos obligatorios erróneos o vacíos:
Nombre de la prueba: Insertar una entidad
Módulo(s): Gestión
Precondiciones esperadas: Usuario autentificado en el sistema
Precondiciones reales: Usuario autentificado en el sistema
Datos de entrada: Entidad con campos obligatorios faltantes como su dirección
Resultado esperado: Error de inserción por datos incompletos o incorrectos 
Resultado real: Error de inserción por datos incompletos o incorrectos

#### Prueba de la geolocalización de IP con un IP de Internet:
Nombre de la prueba: Geolocalizar direcciones IP
Módulo(s): Geográfico
Precondiciones esperadas: El usuario a ejecutado una operación en el sistema
Precondiciones reales: El usuario a ejecutado una operación en el sistema
Datos de entrada: Dirección IP de internet válida
Resultado esperado: Se ha podido geolocalizar con éxito
Resultado real: Se ha podido geolocalizar con éxito

#### Prueba de la geolocalización de IP con un IP de Wifi o Ethernet:
Nombre de la prueba: Geolocalizar direcciones IP
Módulo(s): Geográfico
Precondiciones esperadas: El usuario a ejecutado una operación en el sistema
Precondiciones reales: El usuario a ejecutado una operación en el sistema
Datos de entrada: Dirección IP de Ethernet o Wifi local
Resultado esperado: No se ha podido geolocalizar la IP
Resultado real: No se ha podido geolocalizar la IP

### Prueba de integración de software:
Las pruebas de integración de software son la herramienta que conjunta cada uno de los módulos de un sistema para comprobar su funcionamiento entre sí. Este tipo de test se realizan en las primeras etapas, después de las pruebas unitarias, en las que se analiza un fragmento del código fuente.

#### Big Bang:
Una prueba de integración Big Bang concentra todos los módulos de un sistema para comprobar su funcionamiento en conjunto, por lo que, antes de ejecutarse, el desarrollador debe cerciorarse que cada unidad ha sido completada.  
Este tipo de test es viable en proyectos pequeños, de lo contrario, se pueden pasar por alto errores significativos.
Como se pudo observar en varias pruebas de caja negra, los microservicios funcionan bien al trabajar entre ellos y dan los resultados esperados. Como se puede ejemplificar en el siguiente diagrama de actividad (Figura 9) la comunicación entre los microservicios. 

### Pruebas de concurrencia o pruebas de carga
Se realizaron pruebas de concurrencia para evaluar el comportamiento del sistema con múltiples usuarios simultáneos, específicamente con hasta 4 personas. Los resultados obtenidos fueron los siguientes:
1. Respuesta del Servidor: El servidor gestionó correctamente las solicitudes de todos los usuarios, aplicando las restricciones de acceso adecuadas según los roles asignados a cada uno. No se detectaron fallos en la autorización o autenticación de los usuarios concurrentes.
2. Tiempo de Respuesta: El tiempo promedio de respuesta para las peticiones realizadas a través de Postman fue de aproximadamente 5 a 10 segundos con 4 usuarios activos. Aunque el servidor respondió correctamente a todas las solicitudes, se observó que este tiempo es superior al deseado para un sistema moderno.
3. Coordinación entre Microservicios: Los microservicios del sistema interactuaron de manera eficiente y conjunta, proporcionando las respuestas esperadas a las solicitudes de los usuarios. Esto demuestra una correcta integración y coordinación entre los diferentes componentes del sistema.
4. Balanceo de Carga: Se implementó una prueba de balanceo de carga utilizando un segundo ordenador como nodo adicional. Esta configuración redujo el tiempo de respuesta de las peticiones, lo que indica que el sistema es capaz de mejorar su rendimiento mediante la distribución de la carga entre múltiples servidores.
