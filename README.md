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
   
 3. La salida del comando aws elb describe-load-balancers --load-balancer-name tu_nombre_de_usuario en AWS CLI proporcionará información detallada sobre el balanceador de carga especificado.
   
      Creamos dos instancias EC2, cada una ejecutando un servidor web Apache. Emite lo siguiente.
      ***aws ec2 run-instances --image-id ami-d9a98cb0 --count 2 --instance-type t1.micro --key-name tu_nombre_de_usuario-key --security-groups tu_nombre_de_usuario --user-data file://./apache-install --placement         AvailabilityZone=us-east-1d***
   
      ¿Qué parte de este comando indica que deseas dos instancias EC2? ¿Qué parte de este comando garantiza que tus instancias tendrán Apache instalado? ¿Cuál es el ID de instancia de la primera instancia? ¿Cuál es       el ID de instancia de la segunda instancia?
      
      ![Imagen5](https://user-images.githubusercontent.com/118635410/245156057-5edac939-0f3f-45a2-9676-16ac9b0cbed5.png)
      La parte que indica que deseas dos instancias EC2 es --count 2. Esto especifica que se deben lanzar dos instancias.
      La parte que garantiza que tus instancias tendrán Apache instalado es --user-data file:/home/ubuntu/./apache-install. Aquí se especifica que se debe ejecutar el script apache-install al iniciar las instancias,       lo que instalará Apache automáticamente.

      Para obtener el ID de la primera instancia, usaremos el siguiente comando: ***aws ec2 describe-instances --query  'Reservations[0].Instances[0].InstanceId'
      
      ![Imagen4](https://user-images.githubusercontent.com/118635410/245154371-2431724e-5fa4-4f22-86c1-e9801d357e2a.png)
  
      Para obtener el ID de la segunda instancia, usaremos el siguiente comando: ***aws ec2 describe-instances --query  'Reservations[1].Instances[0].InstanceId'
      
      ![Imagen6](https://user-images.githubusercontent.com/118635410/245157387-7bcf14e9-561e-4b30-8300-d7d40e245d18.png)    
 4. Para usar ELB, tenemos que registrar las instancias EC2. Haz lo siguiente, donde instance1_id e instance2_id son los obtenidos del comando en el paso 3.
    ***aws elb register-instances-with-load-balancer --load-balancer-name tu_nombre_de_usuario --instances instance1_id instancia2_id
    ¿Cuál es la salida?
    
    ![Imagen7](https://user-images.githubusercontent.com/118635410/245159279-3f2a72e9-bd9c-4701-9b54-37fe5de7d58d.png) 
    Ahora vea el estado de la instancia de los servidores cuya carga se equilibra. 
    ***aws elb describe-instance-health --load-balancer-name tu_nombre_de_usuario ¿Cuál es la salida?
    
    ![Imagen8](https://user-images.githubusercontent.com/118635410/245160343-e01bb2be-e21d-41fe-9788-d49ed52a3227.png) 
 
 5. Abre el navegador del sandox. Recupera la dirección IP de tu balanceador de carga del paso 1, ingresa la URL http://nombre_dns_de_tu_balanceador_carga/ en tu navegador web. ¿Qué apareció en el navegador?

    ![Imagen9](https://user-images.githubusercontent.com/118635410/245161608-cb6efd73-6664-4c10-975b-8aa72db20f63.png) 
 
 6. Abre dos ventanas de terminal adicionales y ssh en ambos servidores web. En cada uno, cd al directorio DocumentRoot (probablemente /usr/local/apache/htdocs) y modifique la página de inicio predeterminada,           index.html, de la siguiente manera.

    <html><body><h1>¡Funciona!</h1>
    <p>La solicitud se envió a la instancia 1.</p>
    <p>La solicitud fue atendida por el servidor web 1.</p>
    </body></html>
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
    
    ![Imagen13](https://user-images.githubusercontent.com/118635410/245174745-93dbc424-6ca3-41f1-a4b3-2d9ba8e79be3.png)


    

