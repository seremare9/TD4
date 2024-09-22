# Chapter 2: Application Layer

## 2.1 - Principles of Network Applications

[Video](https://www.youtube.com/watch?v=abeupgK5z48) / [Knowledge checks](https://gaia.cs.umass.edu/kurose_ross/knowledgechecks/problem.php?c=2&s=1)

### Modelo de las OSI (Open Systems Interconnection)

![Captura de pantalla 2024-08-20 a la(s) 21.55.56.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-20_a_la(s)_21.55.56.png)

### Aplicaciones en red

- Software que corre en distintos hosts
- La comunicación se da a través de la red
- Por ejemplo, un browser web (Chrome) se comunica con un servidor web
- No se requiere el desarrollo de software para dispositivos en el núcleo de la red! Estos dispositivos (routers/switches) no corren aplicaciones de usuario
- Las apps en los hosts permiten agilizar su desarrollo y adopción.

![Captura de pantalla 2024-08-20 a la(s) 21.54.34.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-20_a_la(s)_21.54.34.png)

### Arquitectura de aplicaciones

- Determina la estructura de la aplicación en diferentes end systems
- Dos paradigmas: **cliente-servidor** y **peer-to-peer (P2P)**

### Paradigma cliente-servidor

**Servidor**

- **Host siempre disponible** que atiende requests de los clientes → **centralización** de las tareas
- **Dirección (IP) permanente**
- Suelen estar en datacenters → $$

**Cliente**

- Un cliente siempre puede contactar al server enviando un paquete a su dirección IP
- No siempre conectado
- Puede tener direcciones dinámicas
- No se comunican entre sí

### Paradigma peer-to-peer (P2P)

- No hay un servidor
- Comunicación directa entre ***peers** →* hosts interconectados, controlados por usuarios
- Los peers solicitan/ofrecen servicios a otros peers
- Auto-escalabilidad
- Los peers tienen conectividad intermitente; cambian direcciones IP
- Estructura **descentralizada** → problemas de seguridad, performance y fiabilidad

![Captura de pantalla 2024-08-16 a la(s) 14.33.26.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-16_a_la(s)_14.33.26.png)

### Comunicación de procesos

- **Proceso**: Programa en ejecución en un host
- En un mismo host, dos procesos se comunican vía mecanismos de IPC (a nivel sistema operativo)
- Los procesos en diferentes hosts se comunican vía intercambio de **mensajes**
    - El **proceso cliente** crea y envía un mensaje a la red
    - El **proceso servidor** recibe el mensaje y puede responder con otro mensaje
- **Proceso cliente:** el que inicia la comunicación
- **Proceso servidor:** el que espera a ser contactado
- En arquitecturas P2P hay procesos clientes y procesos servidores (en el mismo host)

### Interfaz entre el proceso y la red

- Los procesos envían y reciben mensajes en la red a través de **sockets →** Interfaz de comunicación que oficia como una **puerta**
- El proceso emisor despacha mensajes por la puerta. Del otro lado del socket, el protocolo de transporte obtiene el mensaje y lo envia al socket del proceso receptor.
- Dos sockets involucrados: uno en cada extremo

![Captura de pantalla 2024-08-20 a la(s) 22.56.33.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/6249d9d1-3dd7-40b1-9932-00372389bed4.png)

- Existen tres tipos de sockets:
    - *Datagram sockets*: Se montan sobre el protocolo de transporte **UDP** (no confiable)
    - *Stream sockets*: Se montan principalmente sobre **TCP** (confiable y orientado a conexión)
    - *Sockets raw*: Permiten envío directo de paquetes IP → Mecanismo para implementar protocolos de transporte a nivel usuario (no kernel del SO)

### Puertos y direccionamiento de procesos

- Para intercambiar mensajes, es necesario identificar procesos. El i**dentificador** incluye:
    - La **dirección IP** del host, de 32 bits
    - El **puerto** asociado al proceso receptor corriendo en el host
        - Algunos puertos conocidos:
            - Servidor HTTP: 80
            - Servidor de mail: 25

### Protocolos de aplicación

Especifican:

- Tipos de mensajes (ej: request, response)
- Sintaxis de mensajes (estructura, campos y tamaños)
- Semántica de mensajes (cómo interpretar los datos en los campos
- Reglas que determinan cuándo y cómo los procesos envían y responden mensajes

**Protocolos abiertos**

- Definidos en RFCs; todos tienen acceso a las especificaciones
- Permite interoperabilidad
- ej: HTTP, SMTP

**Protocolos propietarios**

- ej: Skype, Zoom

### Servicios requeridos del nivel de transporte

¿Qué servicios puede ofrecer un protocolo de la capa de transporte a las aplicaciones que lo invocan?

- **Integridad (*reliable data transfer*):** Garantizar que los datos se envían correctamente y no hay packet loss.
    - Algunas apps (e.g., transferencia de archivos o transacciones web) necesitan transferencias confiables. Otras (e.g., audio) pueden tolerar pérdidas
- **Delay:** Garantía de tiempo
    - Algunas apps (e.g., telefonía IP, videojuegos) necesitan bajo delay
- **Throughput:** Cuanto más throughput, mejor
    - Algunas apps (e.g., multimedia) necesitan un mínimo de throughput para ser efectivas (**sensibles al ancho de banda***)*
    - Otras pueden emplear el throughput disponible indistintamente (**elásticas**)
- **Seguridad:** Encriptación de datos, integridad, *end-point authentication*

### Servicios de la capa de transporte: Internet

El Internet ofrece dos protocolos de transporte a las aplicaciones: **UDP y TCP**

**Servicio de TCP**

- Transporte **confiable**
- **Control de flujo**: evita saturar al receptor
- **Control de congestión**: regula la tasa de emisión en función de la carga en la red
- Etapa de reserva de recursos; pre-comunicación (**handshaking**)
- Cuando finaliza el handshaking, se forma una **conexión TCP** entre los sockets de ambos procesos. Cuando la aplicación termina de enviar mensajes, finaliza la conexión
- Sin garantías de tiempo, seguridad o throughput

**Servicio de UDP**

- Transporte **no confiable y sin conexión**
- No hay reserva de recursos
- Los mensajes pueden llegar desordenados
- No ofrece control de flujo ni de congestión
- Sin garantías de tiempo, seguridad o throughput

**Seguridad en TCP**

- Los sockets TCP y UDP no utilizan encriptación → passwords viajan por Internet en texto plano
- **Transport Layer Security (TLS)** *→* Mejora de TCP
    - Provee conexiones TCP encriptadas y ofrece garantías de integridad y autenticación.
    - El proceso emisor envía datos sin encriptar al socket TLS; TLS encripta estos datos y los envía al socket TCP emisor. Los datos viajan por el Internet y llegan al TCPA socket receptor, se envían a TSL y son desencriptados y enviados al proceso receptor mediante el socket TSL. De esta forma, **los datos enviados al socket TLS viajan por Internet encriptados** y a salvo.
    
    **Comparamos las garantías que ofrece cada protocolo**
    
    | *Protocolo* | Integridad | Tiempo | Throughput | Seguridad |
    | --- | --- | --- | --- | --- |
    | TCP | **✅ Sí** | **❌ No** | **❌ No** | **✅ Solo con TSL** |
    | UDP | **❌ No** | **❌ No** | **❌ No** | **❌ No** |

## 2.2 - La Web y el protocolo HTTP

[Video (Parte 1)](https://www.youtube.com/watch?v=S9GEPaQ1lFs) / [Video 2 (Parte 2)](https://www.youtube.com/watch?v=4M39gEPWPYs) / [Knowledge checks](https://gaia.cs.umass.edu/kurose_ross/knowledgechecks/problem.php?c=2&s=2)

- Una página web consta de **objetos** (archivos), que pueden estar almacenados en distintos servidores web
- Las páginas tienen un archivo HTML base que referencia otros objetos, cada uno direccionable por una URL:
    
    ![Captura de pantalla 2024-08-21 a la(s) 18.08.02.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-21_a_la(s)_18.08.02.png)
    

### HTTP (*HyperText Transfer Protocol)*

- **El protocolo de aplicación de la Web**

- Modelo cliente/servidor
    - **Cliente**: el navegador que solicita, recibe (vía HTTP) y “muestra” los objetos web
    - **Servidor**: el dispositivo que envía objetos (vía HTTP) como respuesta a las solicitudes recibidas

![Captura de pantalla 2024-08-22 a la(s) 12.52.44.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/0452887a-d3f9-4fef-a81f-68d622b4f310.png)

- Utiliza **TCP**
    1. El cliente abre un socket e inicia una conexión TCP al puerto 80 del servidor
    2. El servidor acepta la conexión
    3. Se intercambian mensajes HTTP entre el navegador (cliente HTTP) y el servidor web (servidor HTTP)
    4. Se cierra la conexión TCP
- Es ***stateless*** (el servidor no mantiene información de requests anteriores ni de los clientes), ya que los protocolos que preservan estado son complejos (ante una caída del cliente o el servidor, pueden quedar inconsistencias que deben reconciliarse)

### Conexiones HTTP - Persistentes y No Persistentes

En muchas aplicaciones del Internet, el cliente y el server se comunican por un período de tiempo extendido, durante el cuál el cliente hace una serie de requests y el server responde.

- **Conexiones No Persistentes** → Cada par de request/response tiene su propia conexión TCP
    1. Establecimiento de conexión TCP
    2. Envío de **a lo sumo un objeto**
    3. Cierre de conexión TCP
- **Conexiones Persistentes** → Una misma conexión TCP soporta múltiples pares de request/response
    1. Establecimiento de conexión TCP
    2. Envío de **múltiples objetos**
    3. Cierre de conexión TCP

<aside>
👉🏻 **Con HTTP no persistente, la transferencia de múltiples objetos demanda múltiples conexiones**

</aside>

**Por defecto HTTP utiliza conexiones persistentes, pero puede utilizar ambas**

- Ejemplo de conexión no persistente
    
    En la PC se accede a [https://www.utdt.edu/td4/home.html](https://www.utdt.edu/td4/home.html) (contiene texto y referencias a 10 imágenes JPG)
    
    ![Captura de pantalla 2024-08-22 a la(s) 13.47.18.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-22_a_la(s)_13.47.18.png)
    

### **Tiempo de respuesta en HTTP no persistente**

¿Cuánto tiempo pasa desde que el cliente hace el request del archivo HTML base hasta que recibe el archivo completo?

- **RTT (Round Trip Time):** Tiempo que tarda un mensaje (pequeño) en viajar del cliente al servidor y volver. Incluye el delay de propagación del paquete, el delay de encolamiento en packet switches intermediarios y el delay de procesamiento.

- **Tiempo de respuesta (por objeto)**
    - Un RTT para establecer la conexión TCP (***Three way handshake)***
        1. *El cliente envía un segmento TCP al server*
        2. *El server lo reconoce y responde con otro segmento TCP*
        3. *El cliente reconoce la respuesta del server*
    - Otro RTT para el request HTTP y los primeros bytes de la respuesta
        
        El cliente envía el mensaje de request HTTP combinado con la tercera parte del three way handshake (el reconocimiento) a la conexión TCP. Cuando el mensaje de request llega al server, el server envía el archivo HTML a la conexión TCP
        
    - Tiempo de transmisión del objeto

<aside>
👉🏻 **Tiempo total = 2RTT + tiempo de transmisión del objeto**

</aside>

![Captura de pantalla 2024-08-22 a la(s) 15.00.27.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/efc21f35-649d-410b-a6d1-04d318bcda42.png)

### HTTP no persistente: problemas

- Cada request requiere establecer y mantener una conexión nueva → Sobrecarga del SO por cada conexión
- Demanda 2 RTTs por objeto
- Los navegadores suelen abrir conexiones TCP en paralelo para buscar los objetos

### HTTP persistente

- El servidor deja la conexión abierta luego de la respuesta → los mensajes subsiguientes se envían sobre la misma conexión
    - Muchas páginas Web residiendo en el mismo server pueden ser enviadas al mismo cliente en una sola conexión TCP. Estas requests de objetos se pueden hacer back-to-back, sin necesidad de esperar las respuestas de requests pendientes (*pipelining*)
- El cliente envía requests en cuanto encuentra objetos referenciados en el HTML
- Sólo un RTT para todos los objetos referenciados

---

Tipos de mensajes HTTP → Requests y responses

### Requests HTTP

Formato del request HTTP → caracteres ASCII (texto, legible por humanos)

![Ejemplo de mensaje de request](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/10112aa1-b09e-4d42-b0f7-23dbaa9c87c0.png)

Ejemplo de mensaje de request

![Request HTTP: estructura](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-22_a_la(s)_15.33.13.png)

Request HTTP: estructura

### Métodos HTTP

| Método | Cuándo se utiliza |
| --- | --- |
| **GET** | Se usa cuando el browser hace request de un objeto (identificado en la URL). Puede incluir parámetros en la URL del request (luego de un caracter ?). |
| **POST** | Se usa cuando el usuario ingresa un input para su request (este se envía en el body del mensaje). Por ejemplo, cuando llena un form. |
| **HEAD** | Similar a GET. Cuando el server recibe una request HEAD, responde con un mensaje HTTP que no incluye el objeto solicitado. Se suele utilizar para *debugging*. |
| **PUT** | Sube un nuevo archivo al servidor. Reemplaza al archivo de la URL con el contenido en el body del request. |
| **DELETE** | Permite a un usuario o a una aplicación eliminar un objeto del server. |

### Responses HTTP

![Ejemplo de mensaje de response](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/b7d63a6c-1693-48f5-bd03-27ddec000433.png)

Ejemplo de mensaje de response

![Response HTTP: estructura](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-22_a_la(s)_17.13.32.png)

Response HTTP: estructura

### Códigos de status de Response HTTP

- **200 OK**: El request fue exitoso; el objeto se transmite en el mensaje
- **301 Moved Permanently**: El objeto solicitado cambió de ubicación (la misma se indica en el header Location:)
- **400 Bad Request**: El mensaje del request no pudo ser interpretado por el servidor
- **404 Not Found:** El objeto solicitado no pudo encontrarse en el servidor
- **505 HTTP Version Not Supported:** La versión de HTTP solicitada no es soportada por el servidor

---

### 🍪 Cookies

- Las interacciones HTTP son stateless
- Los sitios web y los navegadores utilizan **cookies** para mantener estado entre requests
- Cuatro componentes:
    1. **Header** **Set-Cookie** en las respuestas HTTP → Contiene el identificador del usuario
    2. Header Cookie en el request subsiguiente
    3. Archivo con la cookie almacenado en el host y administrado por el navegador
    4. Base de datos en el sitio web
- Pueden utilizarse para autorización, carritos de compra, recomendaciones, estado de sesión…

![Captura de pantalla 2024-08-22 a la(s) 18.04.23.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-22_a_la(s)_18.04.23.png)

- Cuando un nuevo usuario ingresa a un sitio que utiliza cookies, el server de este sitio crea un id único y genera una entrada en su base de datos que se indexa con ese id.
- De ahí en adelante, cuando ese usuario hace una request en ese sitio, su navegador consulta su cookie file, extrae su id para ese sitio y coloca un header cookie que incluye su id en la request HTTP. De esta forma, el server del sitio puede rastrear la actividad del usuario en el sitio.
- Las cookies pueden tener algunas implicancias en la **privacidad** de los usuarios
    - En particular, las tracking cookies las administra un tercero (distinto al
    sitio al que accedemos) y pueden revelar por ejemplo hábitos de navegación (qué sitios se visitaron y en qué orden)
    

---

### Web Caching

- Una **caché Web** o **proxy server** es una red que satisface requests HTTP en reemplazo del servidor de origen
- La caché Web tiene su propio almacenamiento de disco y **guarda copias de objetos recientemente solititados**
- El navegador de un usuario puede configurarse para que todas sus requests HTTP sean primero dirigidas a la caché.

1. El navegador establece una conexión TCP con la caché y solicita el objeto
2. La caché chequea si tiene una copia de ese objeto en su almacenamiento local
    - Si el objeto está en la caché: se le devuelve al cliente
    - Si no, la caché solicita el objeto del servidor original, lo cachea (guarda una copia) y lo devuelve al cliente

![Captura de pantalla 2024-08-24 a la(s) 10.58.40.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-24_a_la(s)_10.58.40.png)

La caché es **servidor y cliente** al mismo tiempo

- Cuando recibe requests y envía responses a un navegador, es un servidor
- Cuando envía requests y recibe responses de un servidor de origen, es un cliente

**Ventajas de las caché**

- ✅ Reducen tiempo de respuesta al cliente, particularmente si el *bottleneck bandwidth* entre el cliente y el servidor de origen es más pequeño que el *bottleneck bandwidth* entre el cliente y la caché (es decir, la capacidad de transmisión entre el cliente y la caché es mayor)
- ✅ Reducen tráfico en enlaces de acceso a servidores (y en Internet en general)
    - El dueño del enlace no tiene que mejorar su bandwidth → Se reducen los costos
    - Se mejora la performance de otras aplicaciones que acceden al Internet
    

---

### GET Condicional

- Mecanismo de HTTP que se asegura de que las copias de la caché están actualizadas
- **Evita enviar objetos si la caché posee una versión actualizada** (evita delay de transmisión y uso de recursos de red)
- Cliente: indica la fecha de la copia cacheada en un header del request *(If-modified-since)*
- Servidor: la respuesta no contiene el objeto si la copia está actualizada:
*HTTP/1.0 304 Not Modified*

![Captura de pantalla 2024-08-24 a la(s) 11.18.25.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-24_a_la(s)_11.18.25.png)

---

### Versiones HTTP y  *Head-Of-Line Blocking* (HOL)

**HTTP/1.1**

- Introdujo múltiples GETs en pipeline sobre una misma conexión TCP (con el fin de reducir delays en requests HTTP de múltiples objetos)
    
    Recordar: HTTP usa **conexiones TCP persistentes**, permitiendo a una página Web ser enviada de servidor a cliente mediante una sola conexión TCP
    
- El servidor **responde en orden** a los GETs (FCFS: *first come, first served*)
- De este modo, los objetos pequeños podrían quedar esperando hasta ser transmitidos si hay objetos grandes antes (**head-ofline blocking, HOL**)
- Las retransmisiones ante pérdidas de paquetes frenan la transmisión de objetos
    
    ![Captura de pantalla 2024-08-24 a la(s) 11.26.29.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-24_a_la(s)_11.26.29.png)
    

**HTTP/2** → Aumentó la flexibilidad en los servidores al enviar objetos a los clientes:

- Métodos, códigos de status y la mayor parte de los headers permanecen sin cambios respecto de HTTP/1.1
- **Se dividen los objetos en frames**; estos **se acomodan para mitigar el problema de HOL blocking**
- **El orden de transmisión de objetos se basa en prioridades definidas por el cliente**
- Envío de objetos no solicitados al cliente (server push)
    
    ![Captura de pantalla 2024-08-24 a la(s) 11.27.55.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-08-24_a_la(s)_11.27.55.png)
    

HTTP/2 sobre una misma conexión TCP implica:

- Retransmisiones por pérdida de paquetes pueden frenar transmisiones de objetos
    - Como en HTTP/1.1, los navegadores pueden abrir múltiples conexiones en paralelo para reducir estas demoras e incrementar el throughput
- Además, no ofrece seguridad sobre la conexión TCP subyacente

**HTTP/3** → Agrega seguridad, control de errores y control de congestión por objeto; se monta sobre **UDP** → Se implementa un nuevo protocolo de transporte en nivel de aplicación: **QUIC**

## 2.3 - Electronic Mail in the Internet *(*Protocolo SMTP)

E-mail: tres componentes principales:

1. **User Agent**
- Permite leer, responder, reenviar, guardar y redactar mensajes (ej: Outlook)
- Envía el mensaje del usuario al servidor, donde este es almacenado.
1. **Servidores de mail:** En ellos tenemos:
- El buzón (**mailbox**) que guarda los mensajes entrantes para el usuario
- Una **cola de salida** de los mensajes a ser enviados

### 3. Protocolo SMTP

- Transfiere mensajes desde el servidor de mail del emisor al servidor de mail del receptor
    - **Cliente: el servidor de mail emisor**
    - **Servidor: el servidor de mail receptor**
- Utiliza TCP para transferencias confiables
- Interacciones de tipo comando/respuesta (como HTTP) → Mensajes de 7 bits de caracteres ASCII

![Escenario: envío y lectura de mails](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_09.19.35.png)

Escenario: envío y lectura de mails

### Interacción SMTP

1. El cliente SMTP usa TCP para establecer una conexión al **puerto 25** en el server SMTP (si el server se cayó, el mensaje se guarda y se vuelve a intentar después).
2. Handshaking entre el cliente y el server. El cliente indica las direcciones e-mail del emisor y el receptor
3. Transferencia de mensaje(s) persistente
4. Cierre

![Captura de pantalla 2024-09-02 a la(s) 09.27.27.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_09.27.27.png)

- El cliente utiliza distintos **comandos** (HELO, *MAIL FROM, RCPT TO, DATA, y QUIT)*
- El server responde a cada comando. Cada respuesta tiene un **código de reply** y una explicación (opcional)
- El cliente envía una linea con un punto que indica el fin del mensaje al server.

### Formato de mensajes

- Líneas de header: *To: , From: , Subject:*
    - Diferentes a los comandos SMTP MAIL FROM:, RCPT TO, que son parte del handshaking. El header es parte del mensaje en sí.
- Body: el mensaje en sí mismo; sólo caracteres ASCII permitidos

![Captura de pantalla 2024-09-02 a la(s) 09.46.02.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_09.46.02.png)

### SMTP vs. HTTP

- **HTTP: pull del cliente | SMTP: push del cliente**
- Ambos tienen interacciones comando/respuesta en ASCII y códigos de status
- HTTP: cada objeto encapsulado en su propio mensaje de respuesta
- SMTP: múltiples objetos enviados en mensaje multiparte (protocolo MIME)
- **SMTP usa conexiones persistentes**
- SMTP requiere que el mensaje completo (header y body) estén en ASCII
- El servidor SMTP utiliza CRLF.CRLF para determinar el fin del mensaje

### Protocolos de acceso al mail

![Captura de pantalla 2024-09-02 a la(s) 09.52.26.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_09.52.26.png)

SMTP permite entregar y almacenar mensajes de e-mail.

Los protocolos de acceso **permiten obtener los mails de los servidores**

- **IMAP: Internet Mail Access Protocol** → Provee recuperación de mails, borrado y administración de carpetas, entre otros (los mensajes están almacenados en el servidor)
- **HTTP** → Gmail y Hotmail, por ejemplo, ofrecen una interfaz web sobre SMTP para enviar
mensajes. Gmail soporta IMAP (aunque el acceso a los mensajes vía web no se da por este protocolo)

## 2.4 - DNS—The Internet’s Directory Service

Los dispositivos en Internet tienen:

- Una **dirección IP** (32 bits) – utilizada para enviar datagramas
- Un **nombre**, ej., [utdt.edu](http://utdt.edu/) – utilizado por los humanos

**DNS permite mapear una dirección a un nombre y viceversa.**

> La app llama a la función gethostbyname() para hacer la traducción
> 

### Domain Name System (DNS)

- **Base de datos** enorme (miles de millones de registros) distribuida en una jerarquía* de múltiples “servidores de nombres” (name servers)
    
    *A medida que leemos la dirección de izquierda a derecha, obtenemos más info específica sobre la ubicación del host en la red
    
- Protocolo de aplicación: **los hosts se comunican con servidores DNS** para resolver nombres (traducción entre nombres y direcciones)
- Opera a través de mensajes **query and reply** mediante **datagramas UDP** en el **puerto 53**
- Funcionalidad central de Internet: Toda transacción en Internet involucra DNS
- Descentralización organizacional y física: Millones de organizaciones diferentes son responsables de sus propios registros

![Ejemplo de cómo funciona DNS](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_21.00.08.png)

Ejemplo de cómo funciona DNS

Como podemos intuir, DNS agrega delay a las apps de Internet que lo utilizan (a veces se puede reducir con *caching*)

### Servicios y estructura de DNS

- **Aliasing:** Un host con un nombre ****complicado puede tener uno o varios **alias.** En este caso, nos referimos al nombre “real” como **nombre canónico.**

```
relay1.west-coast.enterprise.com (canonical/official website name)
enterprise.com (alias name)
```

- **Aliasing de servidores de mail:** Direcciones de e-mail más fáciles de recordar

```
bob@yahoo.com --> bob@relay1.west-coast.yahoo.com
```

- **Distribución de carga**
    - Para servidores web replicados, un mismo nombre puede estar relacionado a un **set de direcciones IP,** almacenado en la base de datos DNS.
    - Cuando un cliente hace una query DNS para un nombre mapeado a un set de direcciones, el server responde con el set completo. Que el set esté desordenado ayuda a reducir el tráfico, ya que el cliente típicamente envia su request HTTP a la primera dirección que le aparece en el set *(DNS rotation*).

### ¿Por qué no centralizarlo? → No es escalable

- **Punto único de falla:** Si se cae el server DNS, se cae todo el Internet
- **Volumen de tráfico:** Un solo server tendría que atender las billones de queries diarias
- **Distancia:** Un solo server no puede estar “cerca” de todos los querying clients (+ delay)
- **Mantenimiento:** Un solo server debería llevar registro de todos los hosts del Internet

### Jerarquía de servidores DNS

![Captura de pantalla 2024-09-02 a la(s) 21.46.23.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/8a7fe60f-dbf8-4775-ab37-26aa2f6a0283.png)

- Tres clases de servidores:  **`Root`** , **`top level domain (TLD)`** y  **`autoritativos`**.
- Los servers Root proporcionan las direcciones IP de los servers TLD. Los servers TLD proporcionan las direcciones IP de los servers autoritativos. Los servers autoritativos almacenan registros DNS para organizaciones específicas.
- Cada ISP tiene su propio **`servidor DNS local`** → **Caché** para reducir el tráfico y mejorar la performance. No pertenece a la jerarquía.
    - Cuando un host hace una query, esta es enviada al servidor DNS local, que actúa como una caché. En caso de no tener la respuesta, reenvía la query a la jerarquía de servidores DNS. Tener una caché le permite al servidor responder rápidamente a distintas queries para el mismo hostname.
    

---

### Consultas Iterativas y Recursivas

**Consultas Iterativas:** El servidor contactado responde con el nombre del próximo servidor a contactar

![Captura de pantalla 2024-09-02 a la(s) 22.11.53.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_22.11.53.png)

**Consultas Recursivas:** El servidor contactado se encarga de resolver la consulta

![Captura de pantalla 2024-09-02 a la(s) 22.12.39.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_22.12.39.png)

---

### Caching en DNS

Cuando un servidor aprende un nuevo mapeo, lo **cachea** e inmediatamente devuelve este valor cacheado ante futura consultas (aún si no es su servidor autoritativo)

- Los registros cacheados expiran luego de cierto tiempo (*Time To Live, TTL*)
    - Pueden estar desactualizados: Si cierto host cambia su dirección IP, podría permanecer “oculto” hasta que todos los TTLs expiren
- Los servidores TLD suelen estar cacheados en los servidores locales, lo cual permite saltearse los servidores root en una cadena de queries
- DNS ofrece una traducción “best-effort

### Registros DNS

- Los servers DNS almacenan **resource records (RRs**) en la base de datos distribuida
- Formato: `(Nombre, Valor, Tipo, TTL)`
- Tipos de RRs:
    - **`Tipo A`**: Mapea un hostname (nombre) a una dirección IP (valor).
    - **`Tipo NS`**: Mapea un dominio (nombre) al hostname del servidor autoritativo para dicho dominio (valor).
    - **`Tipo CNAME`**: Proporciona el nombre canónico (valor) para cierto alias (nombre)
    - **`Tipo MX`**: Proporciona el nombre canónico (valor) para cierto servidor de mail (nombre)

> Para obtener el nombre canónico del servidor de mail, un cliente DNS consultaría por el registro MX; para obtener el nombre canónico de otro servidor (x ej un servidor web), el cliente consultaría por el registro CNAME.
> 

> Si un server DNS es autoritativo para un hostname particular, el server tendrá un registro de tipo A para el hostname. Si el server NO es autoritativo para un hostname, tendrá un registro de tipo NS para el dominio que incluye el hostname, y un registro de tipo A que proporcione la dirección IP del server DNS en el casillero `Valor`.
> 

### Mensajes DNS

Tanto las queries como las replies tienen el mismo formato.

Header del mensaje:

- **Identificación**: número de 16 bits para la consulta (la respuesta utiliza el mismo)
- **Flags**
    - Query (0) o Reply (1)
    - Recursividad deseada
    - Recursividad disponible
    - Respuesta autoritativa
    

![Captura de pantalla 2024-09-02 a la(s) 23.16.00.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-02_a_la(s)_23.16.00.png)

**Nuevos registros en DNS**
Supongamos que queremos dar de alta el dominio [td4.com](http://td4.com/):

- Debemos registrar dicho nombre en un registrador DNS (e.g., Network Solutions)
    - Proveemos nombres y direcciones IP de servidores autoritativos (primarios y secundarios)
    - El registrador inserta registros NS y A en el servidor TLD de .com:
        
        ```
        ([td4.com](http://td4.com/), [dns1.td4.com](http://dns1.td4.com/), NS)
        ([dns1.td4.com](http://dns1.td4.com/), 111.111.111.1, A)
        ```
        
    - Necesitamos generar un servidor DNS autoritativo con dirección IP 111.111.111.1
        - Debe tener un registro A para [www.td4.com](http://www.td4.com/)
        - Y, por ejemplo, otro de tipo MX (si queremos mails @td4.com)

---

### Seguridad en DNS

**Ataques DDoS**

- Bombardeo de servidores root con tráfico
    - No es muy factible
    - Filtrado de tráfico
    - Los servidores locales cachean las IPs de los servidores TLD (se bypassean los servidores root)
- Bombardeo de servidores TLD: Potencialmente más peligroso

**Ataques de spoofing**

- Intercepción de queries para devolver respuestas falsas
    - DNS cache poisoning: las caches quedan envenenadas, direccionando tráfico a la máquina maliciosa

Más info: pág 135 del libro

## 2.5 - Peer-to-Peer File Distribution

**Recordemos: Arquitectura peer-to-peer (P2P)**

- No hay un servidor
- Comunicación directa entre hosts/peers → **Los peers solicitan/ofrecen servicios a otros peers** → Auto-escalabilidad
- Los peers tienen conectividad intermitente; cambian direcciones IP

**Ejemplo: intercambio de archivos P2P (e.g. BitTorrent)**

### Escabilidad de arquitecturas P2P

- Cliente-servidor: El tiempo de distribución de un archivo aumenta de forma lineal conforme aumenta el número de peers
- P2P: Aumentar el número de peers no aumenta el tiempo de distribución

![Captura de pantalla 2024-09-03 a la(s) 11.28.19.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-03_a_la(s)_11.28.19.png)

### BitTorrent: distribución de archivos P2P

- Protocolo de distribución de archivos P2P
- Los archivos se dividen en **chunks/pieces** de tamaños iguales (típicamente 256 kB).
- **Swarm**: conjunto de peers intercambiando chunks de un torrent (descripción de archivos para distribuir) →  **Cada peer de un swarm envía y recibe chunks de archivos**
- **Tracker:** Lleva registro de los peers participando en un swarm

> ¿Cómo funciona? **Llega un nuevo peer, obtiene la lista de peers del tracker, y comienza a intercambiar pieces con otros peers en el swarm**
> 

Cuando un peer se une a un swarm:

- No posee chunks (los irá acumulando a lo largo del tiempo por medio de los demás peers)
- Se registra en el tracker para obtener un subset de peers e intenta establecer conexiones TCP con ellos. Los **peers vecinos** son aquellos con los que logra establecer la conexión.
- Durante las descargas, los peers suben chunks a otros peers

- **Churn**: los peers pueden venir e irse dinámicamente.
- Los peers pueden cambiar el conjunto de peers con quienes se intercambian chunks
- Cuando un peer obtiene el archivo completo, puede irse (peer “egoísta”) o permanecer en el torrent (peer “altruista”)

![Captura de pantalla 2024-09-03 a la(s) 11.42.27.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-03_a_la(s)_11.42.27.png)

### Pedido de chunks

- En todo instante, distintos peers poseen diferentes subconjuntos de los chunks del archivo
- Periódicamente, cada peer solicita a sus vecinos la lista de chunks que ellos poseen
- A partir de esta info se solicitan los chunks faltantes

![Captura de pantalla 2024-09-04 a la(s) 09.53.01.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/a18e68c3-f64f-44c1-9d34-1812f468d853.png)

- Cómo decido que chunks pedir?
    - **Rarest first** → los más “inusuales” entre sus vecinos primero
    - A veces, la política es random first

### Envío de chunks: tit-for-tat

Un peer envía chunks a los cuatro peers que le envían chunks a mayor velocidad (*unchoked*). Los demás peers no reciben nada (*choked peers*). El “top 4” se reevalúa cada diez segundos

- Cada 30 segundos se selecciona aleatoriamente otro peer y se le envía chunks
    - “Optimistic unchoking”
    - El peer seleccionado puede subirse al “podio” de los top 4
        
        ![Captura de pantalla 2024-09-04 a la(s) 10.21.16.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-04_a_la(s)_10.21.16.png)
        

## 2.6 Video Streaming and Content Distribution Networks

[Video](https://www.youtube.com/watch?v=ak5bbb-xHLI) / Knowledge checks

### Videos en Internet

- El tráfico de streaming es el mayor consumidor de ancho de banda en Internet
- **Desafío:**
    - **Escala – cómo alcanzar a miles de millones de usuarios**
    - Heterogeneidad - cada usuario tiene distintas características (e.j., cableado vs. móvil)

> **Solución: infraestructura distribuida (a nivel de aplicación)**
> 
- Video: **Secuencia de imágenes** mostradas a una tasa constante
- Una imagen (digital) es un **arreglo de píxeles** (cada píxel se representa con bits)
- Codificación: utilizar redundancia dentro de y entre imágenes para **reducir la cantidad de bits del video**
    - Espacial: dentro de la imagen
    - Temporal: de una imagen a la siguiente

### Compresión de video

- **CBR (constant bit rate)**: la tasa de codificación está fija
- **VBR (variable bit rate)**: la tasa cambia de acuerdo a cuánto cambia la codificación espacial/temporal
- Ejemplos:
    - MPEG1 (CD-ROM) 1.5 Mbps
    - MPEG2 (DVD) 3-6 Mbps
    - MPEG4 (usado en Internet; 64Kbps – 12 Mbps)

### Streaming de video almacenado

![Captura de pantalla 2024-09-04 a la(s) 10.33.58.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-04_a_la(s)_10.33.58.png)

- Los videos son almacenados en servers → los usuarios envian requests a esos servers para verlos *on demand.*
- Desafíos principales:
    - El ancho de banda del servidor al cliente puede cambiar a lo largo del tiempo (con niveles de congestión cambiantes)
    - La pérdida de paquetes y el delay por congestión pueden demorar la reproducción del video o deteriorar su calidad
    
    ![Captura de pantalla 2024-09-04 a la(s) 10.34.25.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-04_a_la(s)_10.34.25.png)
    

**Streaming: desafíos**

- **Reproducción continua: el tiempo de reproducción debe coincidir con el tiempo del video original**
- Los delays de la red son variables (jitter), de modo que será necesario hacer **buffering** en el cliente
    - Interactividad: el cliente puede pausar, adelantar, rebobinar, etc.
    - Pérdida de paquetes / retransmisiones
        
        ![El buffering y el delay de reproducción “compensan” el delay de la red](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-04_a_la(s)_10.36.46.png)
        
        El buffering y el delay de reproducción “compensan” el delay de la red
        

### Protocolo DASH (Dynamic Adaptive Streaming over HTTP)

Servidor

- Divide el archivo de video en chunks → Cada chunk se codifica a distintas tasas
- Estas codificaciones se almacenan en archivos distintos
- Los archivos se replican en diversas CDNs
- Un **manifiesto** provee URLs para los distintos chunks

Cliente

- Estima periódicamente el ancho de banda disponible desde el servidor
- **A través del manifiesto, solicita un chunk por vez**
    - **Elige la máxima tasa de codificación sostenible dado el ancho de banda actual**
    - **Puede escoger diferentes tasas en diferentes instantes de tiempo (y también desde distintos servidores)**
- “Inteligencia” en el cliente: determina
    - **Cuándo pedir un chunk** (para evitar buffers vacíos o colapsados)
    - **Cuál tasa de codificación pedir** (mayor calidad cuando haya disponibilidad de mayor ancho de banda)
    - **Dónde pedir un chunk** (puede utilizar la URL de un servidor cercano o con un ancho de banda mayor)

> **Streaming de video = codificación + DASH + buffering**
> 

### Redes de Distribución de Contenido (CDNs)

¿Cómo ofrecer streaming de millones de videos a cientos de miles de usuarios simultáneos?

- ❌ Servidor “enorme”? **NO** → Punto único de falla, congestión, caminos largos (posiblemente congestionados) a muchos clientes distantes, no escala
- ✅ **Alternativa: almacenar múltiples copias de los videos en diversos sitios geográficamente distribuidos – CDNs**

### Componentes de una CDN

![Captura de pantalla 2024-09-04 a la(s) 10.47.05.png](Chapter%202%20Application%20Layer%20cb9f1b87df1848749f6ae07d59170c48/Captura_de_pantalla_2024-09-04_a_la(s)_10.47.05.png)

### Distribución de contenido multimedia

- **Las CDNs almacenan copias del contenido multimedia en sus nodos**
- Los usuarios, al solicitar el contenido, r**eciben un manifiesto del proveedor** (ej. Netflix)
    - Mediante éste, el cliente puede obtener el contenido a la mayor tasa de transferencia soportada
    - Puede elegir una tasa o una copia distinta si ej. hay congestión