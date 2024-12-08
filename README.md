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

## Cuarto paso: Añadir el plugin JaCoCo al proyecto
Modifica el archivo `build.gradle`, de acuerdo con las siguientes instrucciones:
1. Añade el plugin de jacoco.
2. Define la versión de la herramienta.
3. Establece la creación de los informes en los formatos `.xml` y `.html`.
4. Establece para todo el proyecto (`'BUNDLE'`) una cobertura de código del **100%** a nivel de instrucción.
5. Confirma que el check falla, ejecutando el mandato `./gradlew clean check`.

El contenido final de `build.gradle` es el siguiente:
```groovy
/*
 * This file was generated by the Gradle 'init' task.
 *
 * This generated file contains a sample Java application project to get you started.
 * For more details take a look at the 'Building Java & JVM projects' chapter in the Gradle
 * User Manual available at https://docs.gradle.org/7.2/userguide/building_java_projects.html
 */

plugins {
    // Apply the application plugin to add support for building a CLI application in Java.
    id 'application'
    id 'jacoco'
}

repositories {
    // Use Maven Central for resolving dependencies.
    mavenCentral()
}

dependencies {
    // Use JUnit Jupiter for testing.
    testImplementation 'org.junit.jupiter:junit-jupiter:5.11.3'

    // This dependency is used by the application.
    implementation 'com.google.guava:guava:33.3.1-jre'
}

jacoco {
    toolVersion = "0.8.12" // Última versión estable de JaCoCo
}

jacocoTestReport {
    dependsOn test
    reports {
        xml.required.set(true)
        html.required.set(true)
    }
}

jacocoTestCoverageVerification {
    violationRules {
        rule {
            element = 'BUNDLE' // Cobertura a nivel de proyecto completo
            limit {
                counter = 'INSTRUCTION'
                value = 'COVEREDRATIO'
                minimum = 1.0 // 100% de cobertura
            }
        }
    }
}

// Asegurarse de que la verificación se ejecute después de las pruebas
check.dependsOn jacocoTestCoverageVerification

application {
    // Define the main class for the application.
    mainClass = 'com.example.service.App'
}

tasks.named('test') {
    // Use JUnit Platform for unit tests.
    useJUnitPlatform()
}
```

## Quinto paso: Definir un trabajo de integración continua en Jenkins
Las instrucciones son las siguientes:
1. Crea el token de conexión con **GitHub**. Para ello:
    - en los [Personal access tokens](https://github.com/settings/tokens) de GitHub, pulsa sobre *Generate new token* y, a continuación, pulsa sobre *Generate new token (classic)*.
    - dale un nombre en *Note*.
    - establece la fecha de expiración en *Expiration*.
    - define los permisos en *Select scopes*. Yo he escogido todos. No es lo mejor porque la política a aplicar siempre es la de **privilegios mínimos**, pero tampoco es un drama en esta situación.
    - pulsa sobre *Generate token*.
    - copia y **guarda** el token, porque no volverá a estar disponible.
    - en **Jenkins**, ve a `Administrar Jenkins` > `Security` > `Credentials`.
    - en el dominio global, selecciona `Add Credentials`.
    - escoge **Username with password** en *Kind*.
    - el *Username* es tu nombre de usuario en GitHub.
    - pega el token que acabas de crear en el campo *Password*.
    - asigna un ID único, como `github-token`.
    - pulsa sobre *Create*.
2. Configura el **webhook** en GitHub. Para ello:
    - dirígete a tu repositorio y en `Settings` > `Webhooks`, pulsa sobre *Add webhook*.
    - la *Payload URL* es **http://**`Dirección IPv4 asignada`**:8080/github-webhook/**.
    - el *Content type* es `application/json`.
    - deja vacío el campo *Secret*.
    - deshabilita la verificación SSL.
    - en *Which events would you like to trigger this webhook?*, escoge *Let me select individual events.* y marca **Pull requests**.
    - pulsa sobre *Add webhook*.
    - GitHub hará un ping para comprobar que todo está correcto y si todo va bien, entonces en la pestaña *Recent Deliveries* del webhook verás una entrega correcta (código **200 OK**).
3. Crea un **Multibranch Pipeline** en Jenkins. Para ello:
    - instala el *GitHub Integration Plugin*.
    - selecciona `Nueva Tarea`, elige **Multibranch Pipeline** y asígnale un nombre.
    - pulsa sobre *OK*.
    - en *Branch Sources*, pulsa sobre `Add source`, escoge *GitHub*, establece la *Repository HTTPS URL*, selecciona las credenciales que creaste anteriormente y verifica que la conexión es correcta, pulsando sobre `Validate`.
    - en cuanto a los *Behaviours*, consulta la siguiente tabla:

        | Nombre                             | Selección                                        |
        | ---------------------------------- | ------------------------------------------------ |
        | Discover branches                  | All branches                                     |
        | Discover pull requests from origin | The current pull request revision                |
        | Discover pull requests from forks  | The current pull request revision **&** Everyone |
        | Property strategy                  | All branches get the same properties             |

    - en *Build Conguration*, selecciona `by Jenkinsfile` y comrpueba que el *Script Path* es `Jenkinsfile`
4. Crea un `Jenkinsfile`. El `Jenkinsfile` define el pipeline. Aquí tienes un ejemplo:
```groovy
pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }

        stage('JaCoCo Coverage') {
            steps {
                sh './gradlew jacocoTestCoverageVerification'
            }
        }
    }

    post {
        success {
            script {
                updateGitHubStatus('SUCCESS', 'Build and coverage passed')
            }
        }

        failure {
            script {
                updateGitHubStatus('FAILURE', 'Build or coverage failed')
            }
        }
    }
}

def updateGitHubStatus(String state, String description) {
    def context = "continuous-integration/jenkins"
    def commitSha = env.GIT_COMMIT

    withCredentials([usernamePassword(credentialsId: 'github-token', usernameVariable: 'carloslopllo', passwordVariable: 'GITHUB_TOKEN')]) {
        def apiUrl = "https://api.github.com/repos/carloslopllo/example-gradle/statuses/${commitSha}"
        def data = """
        {
            "state": "${state}",
            "description": "${description}",
            "context": "${context}"
        }
        """
        sh "curl -X POST -H 'Authorization: token ${GITHUB_TOKEN}' -d '${data}' ${apiUrl}"
    }
}
```
