

# CI/CD in Azure
>Podrias describir que son y como se implementan CI/CD pipelines, usa Azure como referencia.

**1. Antecedentes**
Antiguamente, el despliegue de software era un evento traumático y manual: un desarrollador compilaba el código en su máquina, generaba un archivo `.zip` o `.msi` y lo copiaba vía FTP al servidor, cruzando los dedos para que no faltara una variable de entorno o una librería. Este proceso, llamado "manual deployment", era lento, propenso a errores humanos y generaba el famoso problema de *"en mi máquina sí funciona"*. CI/CD nace para automatizar este flujo, eliminando la intervención humana en el transporte del código y asegurando que cada cambio sea probado antes de llegar al usuario.

**2. Respuesta técnica**

**CI/CD** es la combinación de dos prácticas: **Integración Continua (CI)** y **Entrega/Despliegue Continuo (CD)**. En el ecosistema de Microsoft, esto se implementa principalmente mediante **Azure DevOps (Azure Pipelines)** o **GitHub Actions**.

### A. Continuous Integration (CI) - "La Fábrica de Calidad"
La CI consiste en automatizar la integración del código de múltiples desarrolladores en un repositorio compartido varias veces al día.

**Flujo de implementación en Azure Pipelines:**
1.  **Trigger:** El pipeline se dispara automáticamente cuando alguien hace un `push` o un `Pull Request` a la rama `main` o `develop`.
2.  **Build (Compilación):** Azure Pipelines levanta un agente (una máquina virtual temporal), descarga el código y ejecuta `dotnet build`.
3.  **Unit Testing:** Se ejecutan las pruebas unitarias (`dotnet test`). Si una sola prueba falla, el pipeline se detiene y el código **no se integra**.
4.  **Static Code Analysis:** Se integran herramientas como **SonarQube** para analizar la calidad del código, detectar vulnerabilidades de seguridad y medir la deuda técnica.
5.  **Artifact Generation:** Si todo pasa, se empaqueta la aplicación en un **Artefacto** (un archivo `.zip` o una imagen de **Docker**) y se almacena en Azure Artifacts.

### B. Continuous Delivery / Deployment (CD) - "La Ruta al Usuario"
La CD toma el artefacto generado en la etapa de CI y lo despliega en los diferentes entornos.

**Flujo de implementación en Azure Pipelines (Releases):**
1.  **Despliegue en Stage (Dev/QA):** El artefacto se despliega automáticamente en un entorno de pruebas. Aquí se ejecutan **Pruebas de Integración** y **Pruebas de Humo (Smoke Tests)**.
2.  **Aprobaciones Manuales (Approval Gates):** En entornos corporativos, antes de pasar a Producción, se requiere que un líder técnico o un QA haga clic en "Aprobar" en el portal de Azure DevOps.
3.  **Despliegue en Producción:** El código se despliega en el entorno final (ej. Azure App Service o Azure Kubernetes Service).

**Estrategias de despliegue avanzado para minimizar el riesgo:**
Para evitar caídas del sistema durante el despliegue, implementaría una de estas dos técnicas en Azure:
*   **Blue-Green Deployment:** Mantengo dos entornos idénticos. Despliego la versión nueva en "Green"; si todo está bien, el balanceador de carga redirige el tráfico de "Blue" a "Green" instantáneamente. Si falla, vuelvo a "Blue" en un segundo.
*   **Canary Releases:** Despliego la versión nueva solo para el 5% de los usuarios. Si las métricas de error se mantienen bajas, despliego al resto progresivamente.

### Resumen de Herramientas en Azure:
*   **Repositorios:** Azure Repos (Git).
*   **Orquestador:** Azure Pipelines (YAML pipelines).
*   **Cómputo:** Azure Agents (Microsoft-hosted o Self-hosted).
*   **Destino:** Azure App Service, Azure Functions o AKS (Kubernetes).
*   **Seguridad:** Azure Key Vault (para manejar secretos y passwords sin que estén en el código).

---
---
---
# CI/CD in AWS
>Ahora la misma pregunta, pero con AWS

**1. Antecedentes**
Al igual que en Azure, la transición hacia CI/CD en AWS busca eliminar el despliegue manual y los errores de configuración. Sin embargo, la filosofía de AWS tiende a ser más modular y basada en servicios especializados que se encadenan entre sí. Mientras que Azure DevOps es una plataforma "todo en uno", en AWS es común combinar herramientas nativas con herramientas externas (como GitHub o GitLab) para construir el flujo de automatización.

**2. Respuesta técnica**

En AWS, el ecosistema de CI/CD se implementa principalmente a través de la suite de **AWS Developer Tools**. El flujo se divide en la fase de construcción y la fase de despliegue.

### A. Continuous Integration (CI) con AWS CodeBuild
La CI en AWS se encarga de transformar el código fuente en un artefacto desplegable.

**Flujo de implementación:**
1.  **Source (Código):** El flujo comienza cuando se detecta un cambio en **AWS CodeCommit**, **GitHub** o **Bitbucket**.
2.  **Build (Construcción):** Entra en juego **AWS CodeBuild**. Este servicio levanta un contenedor temporal donde se ejecuta un archivo llamado `buildspec.yml`.
    *   En este archivo definimos los comandos: `dotnet restore` $\rightarrow$ `dotnet build` $\rightarrow$ `dotnet test`.
3.  **Artifact Storage:** Si el build es exitoso, el resultado (binarios o una imagen de Docker) se almacena en **Amazon S3** o en **Amazon ECR (Elastic Container Registry)** si estamos usando contenedores.

### B. Continuous Delivery / Deployment (CD) con AWS CodeDeploy y CodePipeline
La CD orquestra el movimiento del artefacto desde el repositorio hasta el servidor final.

**Orquestación con AWS CodePipeline:**
**AWS CodePipeline** es el "director de orquesta" que conecta todas las etapas:
`CodeCommit` $\rightarrow$ `CodeBuild` $\rightarrow$ `CodeDeploy`.

**Ejecución del Despliegue con AWS CodeDeploy:**
CodeDeploy es la herramienta que se encarga de instalar el código en los servidores (EC2, Lambda o ECS). Para evitar el tiempo de inactividad, implementaría estas estrategias:
*   **In-Place Deployment:** Actualiza la aplicación en el servidor existente (genera tiempo de inactividad).
*   **Blue/Green Deployment:** Crea un nuevo conjunto de instancias con la nueva versión. Una vez validadas, el **Elastic Load Balancer (ELB)** desvía el tráfico a las nuevas instancias y elimina las antiguas.
*   **Linear/Canary:** Despliega la actualización en porcentajes (ej. 10% cada 10 minutos), monitoreando la salud del sistema mediante **Amazon CloudWatch**.

### Resumen de Herramientas en AWS:

| Etapa            | Herramienta AWS            | Función                                               |
| :--------------- | :------------------------- | :---------------------------------------------------- |
| **Código**       | **AWS CodeCommit**         | Repositorio Git privado.                              |
| **Construcción** | **AWS CodeBuild**          | Compila, prueba y empaqueta el código.                |
| **Despliegue**   | **AWS CodeDeploy**         | Automatiza la instalación en servidores/contenedores. |
| **Orquestación** | **AWS CodePipeline**       | Automatiza el flujo completo (Workflow).              |
| **Contenedores** | **Amazon ECR / ECS / EKS** | Almacenamiento y ejecución de contenedores.           |
| **Monitoreo**    | **Amazon CloudWatch**      | Alertas y logs para disparar *rollbacks* automáticos. |

**Diferencia Estratégica (AWS vs Azure):**
Como Líder Técnico, destacaría que mientras Azure DevOps ofrece una experiencia más integrada y "out-of-the-box", AWS ofrece una granularidad mucho mayor. En AWS, tenemos más control sobre la infraestructura de despliegue, pero requerimos una configuración más detallada de los permisos (**IAM Roles**) y de la orquestación entre servicios.
