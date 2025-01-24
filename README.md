# Jenkins usando Docker como Agent
Este repositorio contiene una configuración básica de un Jenkinsfile donde se utiliza Docker como agent.

Antes de que Docker se usara como agent en Jenkins, los jobs de Jenkins se ejecutaban en máquinas físicas o virtuales configuradas con Jenkins. Las opciones comunes eran:

- Máquinas físicas/virtuales dedicadas: Tenías que provisionar una máquina física o virtual para ejecutar Jenkins o como agente. Esto implicaba mantener y configurar estas máquinas manualmente. Si querías ejecutar trabajos en diferentes entornos, como diferentes versiones de Java o Node.js, tenías que instalar cada versión en cada máquina o virtualizar todo el entorno.

- Máquinas virtuales (VMs):Muchos equipos usaban máquinas virtuales para crear entornos separados. Aunque las máquinas virtuales proporcionan un buen aislamiento, son más pesadas y consumen más recursos que los contenedores Docker. Además, la creación y destrucción de máquinas virtuales para cada build era mucho más costosa en términos de tiempo y recursos.

- Nodos Jenkins permanentes: En lugar de crear y destruir agentes dinámicamente, Jenkins utilizaba nodos permanentes. Estos nodos estaban dedicados a ejecutar trabajos de forma continua, lo que a menudo resultaba en un uso ineficiente de recursos. Los nodos permanentes también requerían mantenimiento manual, como actualizaciones de software o gestión de dependencias.

Resumen:

- Antes de Docker: Jenkins usaba máquinas físicas/virtuales o nodos dedicados para ejecutar trabajos, lo que podía ser costoso, ineficiente en el uso de recursos y requería mucho mantenimiento manual.

- Con Docker como Agent: Los trabajos se ejecutan en contenedores aislados y ligeros, lo que proporciona una mayor escalabilidad, portabilidad, eficiencia en el uso de recursos y facilita la configuración de entornos reproducibles.

----------------------------------------------------------------------------------
# Configuración e instalación 
Para este ejemplo se utiliza Google Cloud, pero se puede usar cualquier recurso de otro proveedor de la nube. En cuanto a los recursos de infraestructura que se van a necesitar son:
- Una instancia
- Habilitar una regla de firewall en el puerto 8080 para que Jenkins quede abierto y disponible para acceso en la instancia (Esta configuración puede variar de proveedor)

# Instalar Jenkins en la instancia
Es necesario tener JDK para que Jenkins se ejecute exitosamente:  
```
sudo apt update
sudo apt install openjdk-17-jre
java -version
```

Ahora a instalar Jenkins:  
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

# Iniciar sesión en Jenkins
Para acceder a Jenkins, es necesaria la ip pública de la instancia que se creó: 
http://IPPUBLICA:8080  

Después de iniciar sesión en Jenkins:

1. Ejecuta el comando para copiar la contraseña de administrador de Jenkins:
   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
2. Ingresa la contraseña de administrador.
3. Presionar sobre "Install suggested plugins"
4. Esperar que Jenkins instale los plugins
5. Crear el primer usuario administrador o saltar este paso (Si planeas utilizar esta instancia de Jenkins para futuros casos de uso, es mejor crear un usuario administrador, por ejemplo admin)

Una vez hecho estos pasos, Jenkins está listo para usarse.

# Instalar el plugin de Docker

- Ve a Manage Jenkins > Manage Plugins.
- En la barra de búsqueda escribe "Docker Pipeline".
- Selecciona el plugin y dale sobre instalar.
- Reinicia Jenkins después de esta instalación.

# Configurar el Docker Slave

Primero hay que instalar Docker:
```
sudo apt update
sudo apt install docker.io
```

Ahora es necesario darle permisos de usuario a Jenkins y a Ubuntu para acceder al deamon de Docker:

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Después de esto, es necesario reiniciar Jenkins otra vez, se puede hacer desde acá:
http://IPPUBLICA:8080/restart

Hasta aquí, el agente de Docker ya está listo para ser usado en los pipelines.
   
