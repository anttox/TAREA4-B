# Amazon ELB - Auto Scaling
# Amazon ELB
Aquí, usamos Amazon Elastic Load Balancing (ELB) y Amazon Cloud Watch a través de la CLI de AWS para equilibrar la carga de un servidor web.
## Parte 1: ELB
1. Inicia sesión en el sandbox del curso AWS . Ve al directorio donde guarda el archivo de script de instalación de apache del laboratorio de la práctica calificada 3. Para crear un balanceador de carga, haz lo siguiente.

aws elb create-load-balancer --load-balancer-name vilca40 --listeners "Protocol=HTTP,LoadBalancerPort=80,InstanceProtocol=HTTP,InstancePort=80" --availability-zones "us-east-1d"

¿Cuál es el DNS_Name del balanceador de carga?
## DNS del balanceador de carga
![Imagen2](https://user-images.githubusercontent.com/118635410/245042275-60394837-51e6-47d8-9381-2b79c497ec70.png)
