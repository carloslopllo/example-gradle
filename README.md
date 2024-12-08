# example-gradle
## Primer paso: Crear una máquina EC2 en AWS, asignándole una IP elástica (*elastic IP*)
Las instrucciones son las siguientes:
1. Inicia sesión en [AWS Academy](https://awsacademy.instructure.com/).
2. Dirígete al [AWS Academy Learner Lab](https://awsacademy.instructure.com/courses/98612).
3. En el menú [Contenidos](https://awsacademy.instructure.com/courses/98612/modules), busca la sección *Laboratorio para el alumnado de AWS Academy* y pulsa sobre [Lanzamiento del Laboratorio para el alumnado de AWS Academy](https://awsacademy.instructure.com/courses/98612/modules/items/9123681).
4. Pulsa sobre *Start Lab*. Los laboratorios tienen una duración de **cuatro** horas. Transcurrido ese tiempo, todo el progreso que hayas realizado se perderá. Sin embargo, dado que nosotros no vamos a utilizar estrictamente el laboratorio, puedes estar tranquilo. Sólo lo necesitamos para lanzar la **AWS Console**.
5. Una vez hayas iniciado el laboratorio, espera a que el círculo situado al lado de *AWS* sea de color verde. Entonces, si pulsas [aquí](https://us-east-1.console.aws.amazon.com/console/home?region=us-east-1#), debería aparecerte la *Página de inicio de la Consola* de AWS.
6. A partir de este punto, en su mayoría, vamos a seguir las indicaciones que figuran en el apartado Moodle [Cómo configurar un servidor Jenkins en AWS](https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/). Para ello:
    - Crea un **grupo de seguridad**, que utilizaremos para definir qué tráfico de red puede entrar y salir de la máquina:
        1. busca *EC2* en el cuadro de búsqueda situado en la parte superior de la *Página de inicio de la Consola*.
        2. en el menú lateral, desplázate a la sección *Red y seguridad* y pulsa sobre *Grupos de seguridad*.
        3. pulsa sobre *Crear grupo de seguridad*.
        4. introduce el nombre del grupo de seguridad, la descripción y dirígete a la sección *Reglas de entrada*. Allí, las reglas que debes añadir son las siguientes:

            | Tipo              | Protocolo | Intervalo de puertos | Origen        |
            | ----------------- | --------- | -------------------- | ------------- |
            | SSH               | TCP       | 22                   | Anywhere-IPv4 |
            | HTTP              | TCP       | 80                   | Anywhere-IPv4 |
            | TCP personalizado | TCP       | 8080                 | Anywhere-IPv4 |

        5. **no** modifiques las *Reglas de salida*.
        6. pulsa sobre *Crear grupo de seguridad*.
        7. si todo ha ido bien, debería aparecerte el mensaje **El grupo de seguridad (**`ID de grupo de seguridad` **|** `Nombre del grupo de seguridad`**) se ha creado correctamente**.
    - Lanza una instancia de una máquina **EC2** de Amazon:
        1. en el menú lateral, desplázate a la sección *Instancias* y pulsa sobre *Instancias*.
        2. pulsa sobre *Lanzar instancias*.
        3. introduce el nombre de la máquina.
        4. asegúrate de que **Amazon Linux** figura como *Imagen de máquina de Amazon*.
        5. selecciona **t2.medium** en *Tipo de instancia*.
        6. escoge **Continuar sin un par de claves (no recomendado)** en *Par de claves (inicio de sesión)*.
        7. selecciona el grupo de seguridad que acabas de crear en la parte **Firewall** de *Configuraciones de red*.
        8. amplia el almacenamiento a **16** GiB gp3.
        9. repasa la configuración en *Resumen* y pulsa sobre **Lanzar instancia**.
        10. si todo ha ido bien, debería aparecerte el mensaje **El lanzamiento de la instancia se inició correctamente (**`ID de la instancia`**)**.
    - Asigna la dirección **IP elástica**:
        1. en el menú lateral, desplázate a la sección *Red y seguridad* y pulsa sobre *Direcciones IP elásticas*.
        2. pulsa sobre *Asignar la dirección IP elástica*.
        3. al final de la página, pulsa sobre *Asignar*.
        4. si todo ha ido bien, debería aparecerte el mensaje **La dirección IP elástica se ha asignado correctamente**.
        5. pulsa sobre *Asociar esta dirección IP elástica*.
        6. en la parte *Instancia*, escoge la máquina que acabas de crear.
        7. pulsa sobre *Asociar*.
        8. si todo ha ido bien, debería aparecerte el mensaje **La dirección IP elástica se ha asociado correctamente. La dirección IP elástica** `Dirección IPv4 asignada` **se ha asociado a instancia** `ID de la instancia`.

## Segundo paso: Instalar Jenkins
Las instrucciones son las siguientes:
1. Conéctate a la máquina **EC2** que creaste en el [primer paso](https://github.com/carloslopllo/example-gradle#primer-paso-crear-una-m%C3%A1quina-ec2-en-aws-asign%C3%A1ndole-una-ip-el%C3%A1stica-elastic-ip). Para ello:
    - en el menú lateral, desplázate a la sección *Instancias* y pulsa sobre *Instancias*. Deberías ver que el *Estado de la instancia* es `En ejecución`.
    - pulsa sobre el `ID de la instancia`. Deberías ver un *Resumen de la instancia*.
    - pulsa sobre el botón *Conectar*.
    - deja la configuración como está y, al final de la página, pulsa sobre *Conectar*. Se te debería abrir una nueva página web en el navegador y, a continuación, una terminal.
2. Descarga e instala Jenkins. Para ello, sigue los pasos en [Downloading and installing Jenkins](https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/#downloading-and-installing-jenkins). Si todo ha ido bien, al final deberías ver un mensaje como el siguiente:
    ```bash
    [ec2-user@ip-172-31-23-136 ~]$ sudo systemctl status jenkins
    ● jenkins.service - Jenkins Continuous Integration Server
        Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled; preset: disabled)
        Active: active (running) since Sun 2024-12-08 14:39:09 UTC; 1min 51s ago
      Main PID: 26504 (java)
         Tasks: 45 (limit: 4657)
        Memory: 688.6M
           CPU: 16.154s
        CGroup: /system.slice/jenkins.service
                └─26504 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080
    ```
3. Configura Jenkins. Para ello:
    - busca **http://**`Dirección IPv4 asignada`**:8080** en el navegador.
    - en la terminal de la máquina, escribe el mandato `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`. El resultado es la *Administrator password*.
    - en la siguiente pantalla, pulsa sobre *Install suggested plugins*.
    - configura el usuario y la contraseña. Por ejemplo:

        | username | password |
        | -------- | -------- |
        | odieste  | 3.1415   |

    - pulsa sobre *Save and Continue*.
    - en la siguiente pantalla, asegúrate de que la *Jenkins URL* es **http://**`Dirección IPv4 asignada`**:8080**.
    - pulsa sobre *Save and Finish*.
    - en la siguiente pantalla, pulsa sobre *Start using Jenkins*.
    - en `Panel de Control` > `Administrar Jenkins` > `Plugins` > `Available plugins`, instala el **Amazon EC2 plugin**. Cuando acabe, pulsa sobre *Volver al inicio de la página*.
    > ⚠️ **Warning**:
    > No sigas los pasos destinados a configurar una nube (*Configure a cloud*) en [Configuring Jenkins](https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/#configuring-jenkins). Nuestras cuentas de usuario no tienen los privilegios necesarios para hacerlo y tampoco es imprescindible para el ejercicio.

## Tercer paso: Crear la estructura inicial del proyecto
Si partes de un directorio no vacío, puedes invocar el mandato `./gradlew wrapper --gradle-version 8.11.1`.
En caso contrario, las instrucciones son las siguientes:
1. Crea un directorio. Por ejemplo, `mkdir example-gradle/`.
2. Cambia al directorio recién creado, mediante `cd example-gradle/`.
3. Ejecuta el mandato `gradle init` y sigue las opciones de configuración que aparecen a continuación:
    ```bash
    vboxuser@Ubuntu:~/example-gradle$ gradle init
    Starting a Gradle Daemon (subsequent builds will be faster)

    Select type of project to generate:
    1: basic
    2: application
    3: library
    4: Gradle plugin
    Enter selection (default: basic) [1..4] 2

    Select implementation language:
    1: C++
    2: Groovy
    3: Java
    4: Kotlin
    5: Scala
    6: Swift
    Enter selection (default: Java) [1..6] 3

    Split functionality across multiple subprojects?:
    1: no - only one application project
    2: yes - application and library projects
    Enter selection (default: no - only one application project) [1..2] 1

    Select build script DSL:
    1: Groovy
    2: Kotlin
    Enter selection (default: Groovy) [1..2] 1

    Select test framework:
    1: JUnit 4
    2: TestNG
    3: Spock
    4: JUnit Jupiter
    Enter selection (default: JUnit Jupiter) [1..4] 4

    Project name (default: example-gradle): example-gradle    

    Source package (default: example.gradle): com.example.service

    > Task :init
    Get more help with your project: https://docs.gradle.org/7.2/samples/sample_building_java_applications.html

    BUILD SUCCESSFUL in 5m 24s
    2 actionable tasks: 2 executed
    ```
Si todo ha ido bien, obtendrás una estructura de directorios como la siguiente:
```bash
vboxuser@Ubuntu:~/example-gradle$ tree .
.
├── app
│   ├── build.gradle
│   └── src
│       ├── main
│       │   ├── java
│       │   │   └── com
│       │   │       └── example
│       │   │           └── service
│       │   │               └── App.java
│       │   └── resources
│       └── test
│           ├── java
│           │   └── com
│           │       └── example
│           │           └── service
│           │               └── AppTest.java
│           └── resources
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── README.md
└── settings.gradle

17 directories, 9 files
```
A continuación, puedes modificar el código de las clases `App.java` y `AppTest.java`. Aquí está el resultado final para `App.java`:
```java
/*
 * This Java source file was generated by the Gradle 'init' task.
 */
package com.example.service;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello, Gradle!");
    }
}
```
y para `AppTest.java`:
```java
/*
 * This Java source file was generated by the Gradle 'init' task.
 */
package com.example.service;

import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

public class AppTest {
    @Test
    public void testApp() {
        assertEquals(1, 1, "1 should equal 1");
    }
}
```
Para ejecutar las pruebas, escribe el mandato `./gradlew test`.
