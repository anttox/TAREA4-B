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
      ***aws ec2 run-instances --image-id ami-d9a98cb0 --count 2 --instance-type t1.micro --key-name tu_nombre_de_usuario-key --security-groups tu_nombre_de_usuario --user-data file://./apache-install --placement      AvailabilityZone=us-east-1d***
   
      ¿Qué parte de este comando indica que deseas dos instancias EC2? ¿Qué parte de este comando garantiza que tus instancias tendrán Apache instalado? ¿Cuál es el ID de instancia de la primera instancia? ¿Cuál es el       ID de instancia de la segunda instancia?
      
      La parte que indica que deseas dos instancias EC2 es --count 2. Esto especifica que se deben lanzar dos instancias.
      La parte que garantiza que tus instancias tendrán Apache instalado es --user-data file:/home/ubuntu/./apache-install. Aquí se especifica que se debe ejecutar el script apache-install al iniciar las instancias,    lo que instalará Apache automáticamente.


