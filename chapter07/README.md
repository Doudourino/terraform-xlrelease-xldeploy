# How do I start the execution?
How do I start the execution of the Terraform templates?

We already have everything necessary to start this deployment. We have the Deployment Package in XL Deploy and the 'environment' that we just created dynamically (configured with a Terraform client and a dictionary with all the parameters that have to be applied).

This will be our third phase in XL Release.

## INFRASTRUCTURE CREATION

We are going to create a third phase in XL Release in which we are going to launch the execution of the templates.

![xlrelease image](img_058.png)

![xlrelease image](img_059.png)

![xlrelease image](img_060.png)

### Step 1: Creation of infrastructure in ${environment} (Core: Sequential Group)

### Step 1.1: Get the latest version deployed (XL Deploy: Get Last Deployed Version)

We have to get the latest version deployed in case we later decide to rollback the applied changes.

![xlrelease image](img_061.png)


### Step 1.2: Creación de infraestructura en AWS (XL Deploy: Deploy)
Se trata de indicar qué queremos desplegar y dónde, es decir, facilitar el Deployment Package y el entorno de destino o environment.
Este script se ejecutará con el CLI para la creación de los recursos necesarios en XLD.

* En `package` tendremos que indicar la versión que queremos desplegar: `${version_infrastructure_selected}` (depende del nombre de la variable que hayamos definido)
* En `environment` el entorno que hemos creado dinámicamente en el paso anterior: `Environments/infrastructure-${project_name}/infrastructure-${project_name}-${environment}/infrastructure-${project_name}-${environment}`

![xlrelease image](img_061.png)

### Paso 2: Obtención de las IPs remotas (XL Deploy CI: Get CI string property)
Cuando se crea la infraestructura con XL Deploy y Terraform, se crean y registran también dos CIs del tipo overthere.SshHost de forma automática en XL Deploy con la información necesaria para acceder a las nuevas instancias EC2 creadas. Esto es, la dirección IP, sistema operativo, usuario y la ubicación de la clave privada para acceder a ellas.

*La clave privada que se facilita como parámetro en la primera fase de XL Release, debe ser accesible desde el servidor en el que se ejecute XL Deploy.*

Con esta tarea, lo que vamos es a recuperar esas direcciones IP para luego poder hacer el provisioning de esas dos instancias EC2.

Sabemos el nombre que van a tener los hosts en XL Deploy (el nombre lo indicamos en las templates), por tanto es muy fácil acceder a los atributos de los mismos.

Nombre de los CI:
* Infrastructure/${project_name}-${environment}-front
* Infrastructure/${project_name}-${environment}-bdd

Property name:
* address *(en ambos casos)*

![xlrelease image](img_062.png)