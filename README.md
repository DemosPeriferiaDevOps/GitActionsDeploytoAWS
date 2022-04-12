## Integración con GitHub Actions- CI/CD Para desplegar uns aplicacion

## Solución

La solución utiliza los siguientes servicios:

1.	[GitHub Actions](https://docs.github.com/en/actions) : Herramienta de orquestación de flujos de trabajo que albergará la canalización. 
2. [AWS CodeDeploy](https://aws.amazon.com/codedeploy/) : Servicio de AWS para gestionar el despliegue en Amazon EC2 Autoscaling Group.
3.	[AWS Auto Scaling](https://aws.amazon.com/ec2/autoscaling/) : Servicio de AWS para ayudar a mantener la disponibilidad y elasticidad de la aplicación añadiendo o eliminando automáticamente instancias de EC2. 
4.	[Amazon EC2](https://docs.aws.amazon.com/ec2/index.html?nc2=h_ql_doc_ec2#amazon-ec2) : Servidor informático de destino para el despliegue de la aplicación.
5.	[AWS CloudFormation](https://aws.amazon.com/cloudformation/) : Servicio de infraestructura como código de AWS (IaC) utilizado para girar la infraestructura inicial en el lado de AWS.
6.	[Proveedor de identidad IAM OIDC](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html) : Servicio de autenticación federada para establecer la confianza entre GitHub y AWS para permitir que las Acciones de GitHub se desplieguen en AWS sin mantener los Secretos y credenciales de AWS. 
7. [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) : Amazon S3 para almacenar los artefactos de despliegue.

El siguiente diagrama ilustra la arquitectura de la solución:
![Alt Text](aws-coodedeplooy-github-action-deploymentV3.png?raw=true "Title")

## Requisitos previos
Antes de comenzar, necesita completar los siguientes requisitos previos:
    
   * Una cuenta de AWS con permisos para crear los recursos necesarios.
   * Un [Cliente Git](https://git-scm.com/downloads) para clonar el código fuente proporcionado.
   * Una [cuenta de GitHub](https://github.com/) con permisos para configurar los repositorios de GitHub, crear flujos de trabajo y configurar los secretos de GitHub.

## Recorrido
Los siguientes pasos proporcionan una visión general de alto nivel del recorrido:

  1.	Clonar el proyecto desde el repositorio de muestras de código de AWS.
  2.	Implementar la plantilla de AWS CloudFormation para crear los servicios necesarios.
  3.	Actualizar el código fuente.
  4.	Configurar los secretos de GitHub.
  5.	Integrar CodeDeploy con GitHub
  6.	Activar la GitHub Actions para construir y desplegar el código.
  7.	Verificar el despliegue.

## Descargar el código fuente

Clona este repositorio aws-codedeploy-github-actions-deployment 

    git clone https://github.com/aws-samples/aws-codedeploy-github-actions-deployment.git

Crea un repositorio vacío en tu cuenta personal de GitHub.

    git clone https://github.com/<username>/<repoName>.git
    
Copia el código. Necesitamos el contenido de la carpeta oculta .github para que las acciones de GitHub funcionen.

    cp -r aws-codedeploy-github-actions-deployment/. <nuevo repositorio>

    por ejemplo, GitActionsDeploytoAWS

## Desplegando la plantilla de CloudFormation
Para desplegar la plantilla de CloudFormation, completa los siguientes pasos:

    1.	Abra la consola de AWS CloudFormation. Introduzca su ID de cuenta, nombre de usuario y contraseña. 
    2.	Compruebe su región, esta solución utiliza us-east-1.
    3.	Si se trata de una nueva cuenta de AWS CloudFormation, haga clic en Create New Stack. De lo contrario, seleccione Crear pila.
    4.	Seleccione La plantilla está lista
    5.	Haga clic en Cargar un archivo de plantilla
    6.	Haga clic en Elegir archivo. Navegue hasta el archivo template.yml en su repositorio clonado en "aws-codedeploy-github-actions-deployment/cloudformation/template.yaml" 
    7.	Seleccione el archivo template.yml y seleccione siguiente.
    8.	En Especificar detalles de la pila, añada o modifique los valores según sea necesario.
            - Nombre de la pila = CodeDeployStack.
            - VPC y subredes = (se han rellenado previamente) puede cambiar estos valores si prefiere utilizar sus propias subredes)
            - GitHubThumbprintList = 6938fd4d98bab03faadb97b34396831e3780aea1
            - GitHubRepoName - Nombre de tu repositorio personal de GitHub que has creado.
    9.	En la página de Opciones, haga clic en Siguiente.
    10.	Seleccione la casilla de reconocimiento para permitir la creación de recursos IAM, y luego seleccione Crear. 
    CloudFormation tardará unos 5 minutos en crear todos los recursos. Esta pila crearía los siguientes recursos
           - Dos instancias EC2 Linux con el servidor Tomcat y el agente CodeDeploy instalado 
           - Grupo de autoescalado con equilibrador de carga de aplicaciones de Internet
           - Nombre de la aplicación CodeDeploy y grupo de despliegue
           - Bucket S3 para almacenar los artefactos de construcción
           - Proveedor de identidad OIDC de Gestión de Identidad y Acceso (IAM)
           - Perfil de instancia para Amazon EC2 
           - Rol de servicio para CodeDeploy
           - Grupos de seguridad para ALB y Amazon EC2

## Configuración y pruebas de GitHub

Para realizar las configuracion completa y realizar las pruebas, puede seguir el [La guía completa](https://aws.amazon.com/blogs/devops/integrating-with-github-actions-ci-cd-pipeline-to-deploy-a-web-app-to-amazon-ec2/) para configurar las acciones de GitHub y probar el flujo de CI/CD.
