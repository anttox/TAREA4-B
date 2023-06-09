# Amazon ELB - Auto Scaling
# Amazon ELB
Aquí, usamos Amazon Elastic Load Balancing (ELB) y Amazon Cloud Watch a través de la CLI de AWS para equilibrar la carga de un servidor web.
## Parte 1: ELB
1. Inicia sesión en el sandbox del curso AWS . Ve al directorio donde guarda el archivo de script de instalación de apache del laboratorio de la práctica calificada 3. Para crear un balanceador de carga, haz lo siguiente.

   ***aws elb create-load-balancer --load-balancer-name tu_nombre_de_usuario --listeners "Protocol=HTTP,LoadBalancerPort=80,InstanceProtocol=HTTP,InstancePort=80" --availability-zones "us-east-1d"***

   ¿Cuál es el DNS_Name del balanceador de carga?
## DNS del balanceador de carga
   ![Imagen2](https://user-images.githubusercontent.com/118635410/245042275-60394837-51e6-47d8-9381-2b79c497ec70.png)
   La opción --listeners define los detalles del oyente del balanceador de carga. En este caso lo configuramos para escuchar el balanceador de carga y las instancias de destino en el puerto 80.

   La opción --availability-zones especifica las zonas de disponibilidad en las que se distribuirá el balanceador de carga.

2. El comando describe-load-balancers describe el estado y las propiedades de tu(s) balanceador(es) de carga. Presenta este comando.

   ***aws elb describe-load-balancers --load-balancer-name tu_nombre_de_usuario***

   ¿Cuál es la salida?!
## Información detallada sobre el balanceador de carga 
   ![Imagen3](https://user-images.githubusercontent.com/118635410/245044524-ed3c1239-30d2-4561-b007-84b63495696d.png)
   
   La salida del comando aws elb describe-load-balancers --load-balancer-name tu_nombre_de_usuario en AWS CLI proporcionará información detallada sobre el balanceador de carga especificado.
   
 3. Creamos dos instancias EC2, cada una ejecutando un servidor web Apache. Emite lo siguiente.
    ***aws ec2 run-instances --image-id ami-d9a98cb0 --count 2 --instance-type t1.micro --key-name tu_nombre_de_usuario-key --security-groups tu_nombre_de_usuario --user-data file://./apache-install --placement         AvailabilityZone=us-east-1d***
   
    ¿Qué parte de este comando indica que deseas dos instancias EC2? ¿Qué parte de este comando garantiza que tus instancias tendrán Apache instalado? ¿Cuál es el ID de instancia de la primera instancia? ¿Cuál es       el ID de instancia de la segunda instancia?
      
    ![Imagen5](https://user-images.githubusercontent.com/118635410/245156057-5edac939-0f3f-45a2-9676-16ac9b0cbed5.png)
    La parte que indica que deseas dos instancias EC2 es --count 2. Esto especifica que se deben lanzar dos instancias.
    La parte que garantiza que tus instancias tendrán Apache instalado es --user-data file:/home/ubuntu/./apache-install. Aquí se especifica que se debe ejecutar el script apache-install al iniciar las instancias,      lo que instalará Apache automáticamente.

    Para obtener el ID de la primera instancia, usaremos el siguiente comando: ***aws ec2 describe-instances --query  'Reservations[0].Instances[0].InstanceId'
      
    ![Imagen4](https://user-images.githubusercontent.com/118635410/245154371-2431724e-5fa4-4f22-86c1-e9801d357e2a.png)
  
    Para obtener el ID de la segunda instancia, usaremos el siguiente comando: ***aws ec2 describe-instances --query  'Reservations[1].Instances[0].InstanceId'
      
    ![Imagen6](https://user-images.githubusercontent.com/118635410/245157387-7bcf14e9-561e-4b30-8300-d7d40e245d18.png)    
 4. Para usar ELB, tenemos que registrar las instancias EC2. Haz lo siguiente, donde instance1_id e instance2_id son los obtenidos del comando en el paso 3.
     ***aws elb register-instances-with-load-balancer --load-balancer-name tu_nombre_de_usuario --instances instance1_id instancia2_id***
    ¿Cuál es la salida?
    
    ![Imagen7](https://user-images.githubusercontent.com/118635410/245159279-3f2a72e9-bd9c-4701-9b54-37fe5de7d58d.png) 
    Ahora vea el estado de la instancia de los servidores cuya carga se equilibra. 
    ***aws elb describe-instance-health --load-balancer-name tu_nombre_de_usuario*** ¿Cuál es la salida?
    
    ![Imagen8](https://user-images.githubusercontent.com/118635410/245160343-e01bb2be-e21d-41fe-9788-d49ed52a3227.png) 
 
 5. Abre el navegador del sandox. Recupera la dirección IP de tu balanceador de carga del paso 1, ingresa la URL http://nombre_dns_de_tu_balanceador_carga/ en tu navegador web. ¿Qué apareció en el navegador?

    ![Imagen9](https://user-images.githubusercontent.com/118635410/245161608-cb6efd73-6664-4c10-975b-8aa72db20f63.png) 
 
 6. Abre dos ventanas de terminal adicionales y ssh en ambos servidores web. En cada uno, cd al directorio DocumentRoot (probablemente /usr/local/apache/htdocs) y modifique la página de inicio predeterminada,           index.html, de la siguiente manera.

    ![Imagen22](https://user-images.githubusercontent.com/118635410/245302454-8066a298-8d6e-4c0c-af8f-ff4abdb52819.png) 
    
    Para el segundo servidor, haz lo mismo excepto que use la instancia 2 y el servidor 2 para las líneas 2 y 3. En el navegador web, accede a tu balanceador de carga 4 veces (actualícelo/recárgalo 4 veces). Esto       genera 4 solicitudes a tu balanceador de carga.
    ¿Cuántas solicitudes atendió el servidor web 1? ¿Cuántas solicitudes atendió el servidor web 2?
    
    Usamos el comando ***ssh -i devenv-key.pem ubuntu@insertamos la ip de nuestra primera id_instancia***, este código también funcionara para la segunda id_instancia, para saber la ip public de nuestra instancia       usamos el comando ***aws ec2 describe-instances --instance-ids insert_your_instance.***
    
    El comando cd /var/www se utiliza como ubicación predeterminada para almacenar los archivos y documentos de un servidor web. En particular, este directorio suele ser el directorio raíz (DocumentRoot) donde se       almacenan los archivos que serán servidos por el servidor web
    
    Una vez dentro abriremos el archivo index.html con el comando vi index.html, realizaremos las modificaciones de su contenido con el siguiente comando, y por último visualizamos en AWS en la parte de                 balanceadores de carga las cantidades de solicitudes que hemos hecho en cada servidor web. Cada servidor web ha realizado 4 solicitudes.
    
    ![Imagen10](https://user-images.githubusercontent.com/118635410/245169370-069e62c5-6b20-4f0f-bb63-163880f49d0b.png)
    
    ![Imagen11](https://user-images.githubusercontent.com/118635410/245169434-de2f85b4-9df4-4053-92c5-62af16c95bf9.png) 
    
 ## Parte 2: CloudWatch
 7. CloudWatch se utiliza para monitorear instancias. En este caso, queremos monitorear los dos servidores web. Inicia CloudWatch de la siguiente manera.
    ***aws ec2 monitor-instances --instance-ids instance1_id instance2_id***
    ¿Cuál es la salida? 
    
    La salida puede proporcionar información sobre las instancias y su estado de monitoreo.
    
    ![Imagen12](https://user-images.githubusercontent.com/118635410/245174745-93dbc424-6ca3-41f1-a4b3-2d9ba8e79be3.png)
    
    Esto indica que el monitoreo se ha habilitado correctamente para las instancias especificadas y su estado se establece en "enabled".
    
    Ahora examina las métricas disponibles con lo siguiente: ***aws cloudwatch list-metrics --namespace "AWS/EC2"***
    ¿Viste la métrica CPUUtilization en el resultado?
    
    ![Imagen13](https://user-images.githubusercontent.com/118635410/245175908-eac3b017-d3b5-4cb5-a945-b871b3d519c3.png)
    
 8. Ahora configuramos una métrica para recopilar la utilización de la CPU. Obtener la hora actual con date -u. Esta será tu hora de inicio. Tu hora de finalización debe ser 30 minutos más tarde. Haz lo siguiente.       aws cloudwatch get-metric-statistics --metric-name CPUUtilization --start-time your_start_time --end-time your_end_time --period 3600 --namespace AWS/EC2 --statistics Maximum – dimensions                             Name=InstanceId,Value=instance2_id 
    ¿Cuál es la salida?
    
    ![Imagen14](https://user-images.githubusercontent.com/118635410/245179658-e87b0e7f-00f2-49e5-958b-03bc4ef31684.png)
    
    Para obtener la hora actual en UTC (Tiempo Universal Coordinado) usaremos el comando date -u. El comando aws cloudwatch get-metric-statistics --metric-name CPUUtilization --start-time your_start_time --end-time     your_end_time --period 3600 --namespace AWS/EC2 --statistics Maximum – dimensions Name=InstanceId,Value=instance2_id  obtendrá la métrica de utilización de la CPU para la instancia con el ID "instance2_id" en el     intervalo de tiempo desde la hora de inicio hasta la hora de finalización.

 9. Apache tiene un herramienta benchmark llamada ab. Si desea ver más información sobre ab, consulte http://httpd.apache.org/docs/2.0/programs/ab.html. Para ejecutar ab, emita el siguiente comando en tu sistema de     trabajo. 
    ab -n 50 -c 5 http://nombre_dns_de_tu_balanceador_carga/
    ¿Qué significan -n 50 y -c 5? ¿Cuál es la salida?.
    
    ![Imagen15](https://user-images.githubusercontent.com/118635410/245181084-67c5ee51-821e-4463-a9a6-92158c3cb0ae.png)
    
    En el comando ab -n 50 -c 5 http://nombre_dns_de_tu_balanceador_carga/, los parámetros -n 50 y -c 5 tienen los siguientes significados:
    -n 50: Especifica el número total de solicitudes que se enviarán al servidor. En este caso, se enviarán 50 solicitudes.
    -c 5: Especifica el número de solicitudes concurrentes que se enviarán al servidor al mismo tiempo. En este caso, se enviarán 5 solicitudes concurrentes.
    La salida de este comando mostrará información sobre el rendimiento de la carga del servidor web. Incluirá estadísticas como el tiempo promedio de respuesta, el tiempo máximo de respuesta, el tiempo total de la     prueba, la velocidad de transferencia, entre otros datos relacionados con el rendimiento y la eficiencia del servidor bajo carga.
    
 10. Ahora queremos examinar la métrica de latencia del ELB. Utiliza el siguiente comando con las mismas horas de inicio y finalización que especificó en el paso 8. 
  
     ***aws cloudwatch get-metric-statistics --metric-name Latency --start-time your_start_time --end-time your_end_time --period 3600 --namespace AWS/ELB --statistics Maximum --dimensions                                    Name=LoadBalancerName,Value=tu_nombre_de_usuario***
     
     ***aws cloudwatch get-metric-statistics --metric-name RequestCount --start-time your_start_time --end-time your_end_time --period 3600 --namespace AWS/ELB --statistics Sum --dimensions
     Name=LoadBalancerName,Value=tu_nombre_de_usuario***
     
     ¿Qué resultados recibió de ambos comandos?
     
     ![Imagen16](https://user-images.githubusercontent.com/118635410/245182516-4e8afdd5-2df9-40a9-9393-a8e063933d91.png)
     
     ![Imagen17](https://user-images.githubusercontent.com/118635410/245182572-ac305262-a4b4-4248-a61a-fc17a8a1addf.png)
     
     El primer comando te proporciona información sobre la latencia máxima experimentada por las solicitudes al balanceador de carga, mientras que el segundo comando te muestra la cantidad total de solicitudes            realizadas al balanceador de carga en un período de tiempo determinado. Estos resultados pueden ayudarte a evaluar el rendimiento y la carga en tu sistema y tomar decisiones basadas en ellos.
 ## Parte 3: Limpieza
 11. Necesitamos cancelar el registro de las instancias de ELB. Haz lo siguiente.
     
     ***aws elb desregister-instances-from-load-balancer --load-balancer-name tu_nombre_de_usuario --instances instance1_id instance2_id***
     
     ¿Cuál es la salida?
     
     ![Imagen18](https://user-images.githubusercontent.com/118635410/245300417-30c9c04a-4b69-4dba-8b7b-ee2dc6db8229.png)
     
     Esto indica que las instancias instance1_id y instance2_id se han desregistrado exitosamente del balanceador de carga especificado.
     
 12. A continuación, eliminamos la instancia de ELB de la siguiente manera. ***aws elb delete-load-balancer --load-balancer-name tu_nombre_de_usuario***
     
      ![Imagen19](https://user-images.githubusercontent.com/118635410/245300993-11e8f591-70c1-468f-b1d7-afa73026bf8b.png)
      
      Finalmente, finaliza las instancias del servidor web de tus instancias y tu instancia EC2. ¿Qué comandos usaste? ¿Cuál es la salida?
      
      Para finalizar las instancias del servidor web (instance1_id e instance2_id) usamos el comando ***aws ec2 terminate-instances --instance-ids your_id_intance1 your_id_instance2***
      
      ![Imagen20](https://user-images.githubusercontent.com/118635410/245301881-ef4e2eed-798c-428b-8eb6-7b697db93d37.png)
      
      Para finalizar la instancia EC2 usamos el siguiente comando ***aws ec2 terminate-instances --instance-ids your_ID_instance_EC2***
      
      ![Imagen21](https://user-images.githubusercontent.com/118635410/245301939-0105c758-9490-4182-a918-9e49df56ac5a.png)

# Auto Scaling
Usamos AWS CLI para configurar sus instancias EC2 para el escalado automático.
## Parte 1: escalar hacia arriba
1. Inicia sesión en el sandbox virtual. Cambia al directorio donde guarda el archivo de script de instalación de apache. Inicie una instancia de la siguiente manera. ***aws autoscaling create-launch-configuration
   --launch-configuration-name tu_nombre_de_usuario-lc --image-id ami-d9a98cb0 --instance-type t1.micro --key-name tu_nombre_usuario-key --security-groups tu_nombre_usuario --user-data file://./apache-install***

   ¿Cuál es la salida?
   
   ![Imagen23](https://user-images.githubusercontent.com/118635410/245304853-08e6fce2-9bd9-4d0d-9f58-909865e3f024.png)
   
   A continuación, crea un equilibrador de carga. ***aws elb create-load-balancer --load-balancer-name tu_nombre_de_usuario-elb --listeners "Protocol=HTTP, LoadBalancerPort=80, InstanceProtocol=HTTP,InstancePort=80"    – availability-zones us-east-1c***
   
   ¿Cuál es la salida?
   
   La salida de este comando será un resultado JSON que contiene información detallada sobre el equilibrador de carga creado, como su nombre, ID, DNS y otras propiedades relacionadas. Usamos el comando ***aws elb      describe-load-balancers --load-balancer-names your_username-elb*** para ver la información detallada de nuestro equilibrador de carga.
   
   # Equilibrador de carga
   
   ![Imagen24](https://user-images.githubusercontent.com/118635410/245307658-8cc29f3a-9523-4fca-862a-9eda93326502.png)
   
   
2. Ahora creamos un grupo de escalado automático. Haz lo siguiente. ***aws autoscaling create-auto-scaling-group --auto-scaling-group-name tu_nombre_de_usuario-asg --launch-configuration-name tu_nombre_de_usuario-lc    --min-size 1 --max-size 3 --load-balancer-names tu_nombre_de_usuario-elb --availability-zones us-east-1c*** 
 
   ¿Cuál es la salida?
   
   La salida de este comando será un resultado JSON que contiene información sobre el grupo de escalado automático creado, incluyendo su nombre, configuración, tamaño mínimo y máximo, equilibradores de carga            asociados, zonas de disponibilidad y más. Usamos el comando ***aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names vilca40-asg*** para visualizar la información sobre el grupo de escalado        automático creado.
   
   ![Imagen26](https://user-images.githubusercontent.com/118635410/245317809-3b20aff6-53ed-4dae-a86c-e442459056ba.png)
   
 3. Para describir el grupo de escalado automático que acabas de crear, emite el siguiente comando. ***aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name tu_nombre_de_usuario-asg***
   ¿Cuál es la salida? Deberías ver que se crea una nueva instancia EC2. Si no lo ves, espera 2 minutos y vuelve a ejecutar el comando. ¿Cuál es el ID de la instancia?
   
   ![Imagen27](https://user-images.githubusercontent.com/118635410/245318110-e01acf30-d44b-4c19-b9c7-2a972c79a15c.png)
   
   Con el comando aws ec2 describe-instances –filters "Name=tag:aws:autoscaling:groupName,Values=vilca40-asg" encontraremos la nueva instancia EC2 creada.
   
   ![Imagen25](https://user-images.githubusercontent.com/118635410/245312184-50cf029d-4852-40bc-8b3d-72107e5a6333.png)
   
 4. Ahora creamos una política de escalado hacía arriba seguida de una alarma de CloudWatch para determinar, en caso de que lapolítica sea cierta, que AWS necesita ampliar nuestros recursos. Escribe los dos comandos     que siguen. AnotA el valor de PolicyARN del primer comando. 
    ***aws autoscaling put-scaling-policy --auto-scaling-group-name tu_nombre_de_usuario-asg --policy-name tu_nombre_de_usuario-scaleup --scaling-adjustment 1 --adjustment-type ChangeInCapacity --cooldown 120*** 
    
    ***aws cloudwatch put-metric-alarm --alarm-name tu_nombre_de_usuario-highcpualarm --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 120 --threshold 70 --comparison-operator                  GreaterThanThreshold --dimensions "Name=AutoScalingGroupName,Value= tu_nombre_de_usuario-asg" --evaluation-periods 1 --alarm-actions value_of_PolicyARN*** 
    
    ¿Cuál es la salida de ambos comandos? ¿Cuál es el valor de PolicyARN? (El valor es una cadena larga sin comillas).  
    
    El comando para crear una política de escalado hacia arriba es el siguiente:
    
    ![Imagen28](https://user-images.githubusercontent.com/118635410/245364432-57658f5b-637b-44f1-ac55-352062193d61.png)
    
    "PolicyARN": "arn:aws:autoscaling:us-east-1:404009776926:scalingPolicy:3df68a10-230b-499e-a01c-bf956e9b25ef:autoScalingGroupName/vilca40-asg:policyName/vilca40-scaleup"
    
    El comando para crear una alarma de CloudWatch es el siguiente:
    
    ![Imagen29](https://user-images.githubusercontent.com/118635410/245364519-c9a8743a-0ee0-4ce9-b895-a2fac088d8be.png)
    
    Ejecuta el siguiente comando para ver una descripción de tu alarma. ***aws cloudwatch describe-alarms --alarm-names tu_nombre_de_usuario-highcpualarm***
    
    ¿Cuál es la salida?

    ![Imagen30](https://user-images.githubusercontent.com/118635410/245364611-af1a9e3d-e756-4807-852b-49ee06ec1f5e.png)
    
 5. Inicia sesión en la instancia EC2 desde el paso 1 mediante ssh. Cambia al root. Cargaremos y ejecutaremos una herramienta stress para aumentar la utilización de procesamiento del servidor. Emite los siguientes       comandos de Linux.
 
    apt-get install stress
    
    stress--cpu 1
    
    ![Imagen31](https://user-images.githubusercontent.com/118635410/245364683-86a3d8f8-b7c0-416f-8443-3edf4afad710.png)
    
    Esto significa que el programa stress está generando carga de trabajo en el procesador de la instancia EC2, lo que debería aumentar la utilización de la CPU. Puedes dejar el comando ejecutándose para continuar       generando carga en el sistema o detenerlo presionando Ctrl+C en la terminal.
    
    Inicia un nuevo terminal. Repite el siguiente comando cada 2 minutos hasta que veas la segunda instancia EC2 y luego una tercera instancia EC2. En este punto, emite las siguientes instrucciones en la ventana de     tu terminal inicial.
    ***aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name tu_nombre_de_usuario -asg***
    
    ¿Cuál es la salida?
    
    ![Imagen32](https://user-images.githubusercontent.com/118635410/245364747-e6a028df-85ba-44c8-b555-abc294b670cf.png)

## Parte 2: reducir la escala
6. Ahora exploraremos cómo AWS puede controlar el escalado hacia abajo mediante la eliminación de algunas de las máquinas virtuales creadas. Ejecuta los siguientes dos comandos, nuevamente tomando nota del PolicyARN    creado a partir del primer comando para usar en el segundo.
   
   ***aws autoscaling put-scaling-policy --auto-scaling-group-name tu_nombre_de_usuario-asg --policy-name tu_nombre_de_usuario-scaledown --scaling-adjustment -1 --adjustment-type ChangeInCapacity --cooldown 120 aws       cloudwatch put-metric-alarm --alarm-name tu_nombre_de_usuario-lowcpualarm --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 120 --threshold 30 --comparison-operator                       LessThanThreshold --dimensions "Name=AutoScalingGroupName,Value=tu_nombre_de_usuario-asg" --evaluation-periods 1 --alarm-actions value_of_PolicyARN***

   ¿Cuáles son las salidas? ¿Cuál es el valor de PolicyARN?
   
   ![Imagen33](https://user-images.githubusercontent.com/118635410/245364843-a2bcba22-d617-4580-bdde-38e9bb1132d1.png)
   
   Usamos el comando aws cloudwatch describe-alarms --alarm-names vilca40-lowcpualarm para ver más información sobre la alarma.
   
   ![Imagen34](https://user-images.githubusercontent.com/118635410/245364915-8adcb5ee-a478-49c5-82c4-788965cac741.png)
   
7. Cambia al terminal de la instancia EC2. Escribe ctrl+c para detener el comando stress. Vuelva a la ventana del terminal y repite el siguiente comando cada 2 minutos hasta que vea solo un EC2 en su grupo de          escalado automático. ***aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name tu_nombre_de_usuario-asg***

   ¿Cuál es la salida?
   
   ![Imagen35](https://user-images.githubusercontent.com/118635410/245364984-c6b89a78-965f-4458-bf78-d18acdda3e72.png)
   
   Repite el siguiente comando cada 2 minutos hasta que vea solo un EC2 en su grupo de escalado automático. Para ello usaremos el comando ***watch -n 120 aws autoscaling describe-auto-scaling-groups --auto-scaling        group-name tu_nombre_de_usuario-asg***. Este comando ejecutará el comando aws autoscaling describe-auto-scaling-groups cada 120 segundos (2 minutos) y mostrará la salida actualizada en tiempo real.
   
## Parte 3: Limpieza
8. Elimina el grupo de escalado automático mediante el siguiente comando. Si el grupo tiene instancias o actividades de escalado en curso, debes especificar la opción para forzar la eliminación para que se realice      correctamente. Si el grupo tiene políticas, al eliminar el grupo se eliminan las políticas, las acciones de alarma subyacentes y cualquier alarma que ya no tenga una acción asociada. Después de eliminar tu grupo    de escala, elimina tus alarmas como se muestra a continuación. ***aws autoscaling delete-auto-scaling-group --auto-scaling-group-name tu_nombre_de_usuario-asg --force-delete*** 
   ***aws cloudwatch delete-alarms --alarm-name tu_nombre_de_usuario-lowcpualarm***
   
   ¿Cuál es la salida?
   
   ![Imagen36](https://user-images.githubusercontent.com/118635410/245365066-1134dcc9-b4a5-4854-bd27-fdf86b30cb29.png)
   
   ![Imagen37](https://user-images.githubusercontent.com/118635410/245365138-9258553f-2633-4816-944f-5353f8d9ff6c.png)
   
   ***aws cloudwatch delete-alarms --alarm-name-tu_nombre_de_usuario-highcpualarm***
   
   ¿Cuál es la salida?
   
   ![Imagen38](https://user-images.githubusercontent.com/118635410/245365176-3997168e-8f91-4fe8-aab4-9c913bf253c2.png)
   
   Elimina tu configuración de lanzamiento de la siguiente manera. ***aws autoescaling delete-launch-configuration --launch-configuration-name tu_nombre_de_usuario-lc***
   
   ¿Cuál es la salida? 
   
   ![Imagen39](https://user-images.githubusercontent.com/118635410/245365220-915164a0-49ce-4d81-865a-dd17ae2d06f5.png)
   
   Finalmente, elimina tu ELB. ¿Qué comando ejecutaste? 
   
   Para eliminar el balanceador de carga de Elastic Load Balancing(ELB) usse el comando ***aws elb delete-load-balancer --load-balancer-name vilca40-elb*** y para verificar use el comando ***aws elbv2 describe-load    balancers.***
   
   ![Imagen40](https://user-images.githubusercontent.com/118635410/245365269-45be5570-fb67-4f8d-87d5-e331df5545d5.png)


##NOTAS

Hubo un error en la explicacion del problema del comando de la parte 3
Creamos dos instancias EC2, cada una ejecutando un servidor web Apache. Emite lo siguiente.
    ***aws ec2 run-instances --image-id ami-d9a98cb0 --count 2 --instance-type t1.micro --key-name tu_nombre_de_usuario-key --security-groups tu_nombre_de_usuario --user-data file://./apache-install --placement         AvailabilityZone=us-east-1d*** 
Hay un error en la parte de  AvailabilityZone=us-east-1d, la zona debia ser us-east-1a. Ambas instancias trabajaban en una zona ue no permitia el trafico. De esa manera se puede solucionar el error de Instance has not passed the configured HealthyThreshold number of health checkks consecutively.

![Imagen41](https://user-images.githubusercontent.com/118635410/245160343-e01bb2be-e21d-41fe-9788-d49ed52a3227.png)
   





 







    

