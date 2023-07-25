# Assessment-cv
El repositorio es para la gestión de CV y certificaciones de los candidatos

# Contexto

El proposiro de la soluición es construir un respositorio centralizado y digital para todos los logros académicos y profesionales de un individuo.
Describiré las funcionalidades clave de la aplicación de la siguiente manera:
Las funcionalidades cubiertas son:

1- **Registro de usuarios:** Para garantizar la seguridad y la privacidad, cada usuario debe crear una cuenta única en la aplicación. El proceso de registro puede incluir campos estándar, como nombre, dirección de correo electrónico, contraseña y tal vez algunos detalles opcionales, como el campo de estudio o área de interés.

2- **Subir documentos académicos del usuario registrado:** Esta función permite a los usuarios cargar digitalmente sus diplomas, certificados y otros documentos académicos o profesionales. Deberíamos tener en cuenta el tipo de formatos de archivo que admitiremos (por ejemplo, PDF, JPG) y posiblemente la opción de añadir descripciones o etiquetas para facilitar la búsqueda y organización de los documentos.

3- **Generar un código QR** donde se podrá observar un PDF con toda la información académica registrada. Este código QR, cuando se escanea, enlaza a un PDF que contiene todos los logros académicos y certificaciones del usuario. 

4- **Generar una página web** donde podrá ver sus certificaciones académicas: Además del código QR, cada usuario tiene una página web personal en la plataforma. Esta página es esencialmente un perfil digital que muestra todas las certificaciones y logros académicos del usuario. 

# Contenedores
![image](https://github.com/jgutierrez-pragmatico/assessment-cv/assets/117243510/db7f01bc-1d8a-4441-bb2b-01b661f0df8f)


La solución consiste en los siguientes contenedores:
- **[X] Front:** Interfaz de usuario para realizar las operaciones (APP)
- **[X] Dominio de Onboarding:** Registra nuevos usuarios en el sistema.
- **[X] Dominio de Documentos:** Esta función permite a los usuarios cargar digitalmente sus diplomas, certificados y otros documentos académicos.
- **[X] Dominio de Administración:** Permite a los usuarios gestionar los features flags "activado" o "desactivado" de los documentos y su perfil.
- **[X] Dominio de QR-Code:** Permite generar el perfil con los documentos digitales y asociar un sitio web dentro del QR code.
- **[X] Dominio de Notificacionies:** Permite enviar notificaciones a los usuarios.
- **[X] Broker de Eventos:** Orquesta la comunicación entre servicios y maneja eventos asíncronos.
- **[X] Servicio de Autenticación**: Maneja la autenticación y autorización de usuarios.

# Componentes
![image](https://github.com/jgutierrez-pragmatico/assessment-cv/assets/117243510/0b656666-e015-4f17-ba08-ccba4157ad7b)


# Descripción de la solución

### Onboarding : Sing-up/sing-in
- El usuario inicia sesión en la aplicación web - (Sin no existe puede registrarse con correo/contraseña o  a través de Oauth2.0 )
- El usuario se crea en el grupo de usuarios de Cognito y los atributos de usuario se rellenan basándose en las asignaciones de atributos (nombres, datos personales y data relevante).
- Tras una autenticación correcta, Cognito recibirá un token de acceso JWT .
- Se devuelve un token JWT de Cognito a la aplicación.
- El front  realiza una llamada al API Gateway.
- La aplicación extrae el token de identificación del JWT y pasa el token en la cabecera de autorización de la API.
- La pasarela de API invoca el autorizador Lambda personalizado y pasa el token para su posterior validación (servicios internos).

### Documentos:
- **Subir documento:** Un usuario puede subir un documento, que se almacenará en un bucket S3. Una vez que el archivo se ha subido correctamente, se genera un evento que desencadena una función Lambda de AWS.
- **Procesar evento de S3 en Lambda:** La función Lambda se activa cada vez que se sube un nuevo documento al bucket S3. Esta función tiene la responsabilidad de procesar el evento, extraer la información necesaria del objeto recién subido y prepararla para el almacenamiento en DynamoDB.
- **Registrar datos en DynamoDB:** Después de procesar el evento, la función Lambda registrará los datos en una tabla de DynamoDB.
  
### Adminsitración
- **Crear/Actualizar Feature Flags:** Los administradores podrán crear o actualizar "feature flags". Los feature flags son una forma de controlar la visibilidad y la accesibilidad de ciertos documentos sin tener que realizar cambios en el código. Por ejemplo, un feature flag podría permitir a los administradores activar o desactivar la visibilidad de ciertos tipos de documentos, como diplomas o certificados de cursos.
- **Administrar Documentos:** Los administradores tendrán la capacidad de administrar todos los documentos en la base de datos. Esto puede incluir tareas como ver, eliminar o modificar la información de los documentos.

### QR Code
- **Generar Código QR**: Una vez que un usuario ha cargado sus documentos, la función Lambda generará un código QR único. Este código QR contendrá las URLs de los documentos del usuario almacenados en S3 y la URL del perfil del sitio web del usuario.
- **Persistir Código QR en DynamoDB:** Una vez generado, el código QR y su información asociada serán persistidos en una tabla DynamoDB. Esta información debe incluir el ID del usuario, las URLs de los documentos, la URL del perfil del sitio web y el código QR.
- **Notificar al Usuario:** Tras la creación exitosa del código QR, se enviará un mensaje a una cola de notificación utilizando AWS SNS. Este mensaje contendrá la información necesaria para enviar un correo electrónico al usuario con su código QR. EL dominio de notificaciones escucha la cola de notificación y envia el correo electrónico correspondiente.

# Estilo de arquitectura 

## Serverless

#### **- Ventajas:**

1 - **Sin administración de servidores:** No es necesario provisionar, mantener o administrar servidores. AWS lo hace por nosotros.

2 - **Escalabilidad automática:** La infraestructura serverless se escala automáticamente en función de la demanda.

3 - **Costo eficiente:** Con el modelo de precios de pago por uso, sólo pagas por el tiempo de ejecución de tus funciones. No pagas por la infraestructura ociosa.

4 - **Alta disponibilidad:** Los proveedores de servicios en la nube ofrecen alta disponibilidad y recuperación ante desastres con sus servicios serverless.


# Observabilidad 

Desacople de componentes: En una arquitectura serverless basada en eventos en AWS, utilizaremos los servicios:

- Con **CloudWatch** supervisamos  métricas de rendimiento de tus funciones Lambda, colas SQS y DynamoDB. crear alarmas que se activan si alguna de estas métricas supera un umbral que podría indicar un problema. (umbrales de procesamiento o memoria)

- Con usar **X-Ray** rastreamos las peticiones que ingresan hasta que se completan, pasando por todas las funciones Lambda, SQS y DynamoDB que se utilizan para procesar la solicitud. Esto nos facilita identificar cuellos de botella y problemas de rendimiento.

- los logs se enviaran desde las funciones Lambda a **CloudWatch Logs.** Estos logs pueden incluir información sobre eventos de transacciones, errores y cualquier otra cosa que pueda ser útil para entender lo que está pasando en tu sistema. con el fin de implementar modelos predictivos

  
