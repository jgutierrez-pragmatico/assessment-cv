# assessment-cv
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
![image](https://github.com/jgutierrez-pragmatico/assessment-cv/assets/117243510/87076ae8-f523-4f21-8d00-8b8ded546d0d)

# Descripción de la solución

### Onboarding : Sing-up/sing-in
- El usuario inicia sesión en la aplicación web - (Sin no existe puede registrarse con correo/contraseña o  a través de Oauth2.0 )
- El usuario se crea en el grupo de usuarios de Cognito y los atributos de usuario se rellenan basándose en las asignaciones de atributos (nombres, datos personales y data relevante).
- Tras una autenticación correcta, Cognito recibirá un token de acceso JWT .
- Se devuelve un token JWT de Cognito a la aplicación.
- El front  realiza una llamada al API Gateway.
- La aplicación extrae el token de identificación del JWT y pasa el token en la cabecera de autorización de la API.
- La pasarela de API invoca el autorizador Lambda personalizado y pasa el token para su posterior validación (servicios internos).
  
