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
