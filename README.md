# Proyecto Kafka Producer/Consumer con Spring Boot

Este proyecto implementa un flujo completo de **Productor** y **Consumidor** de mensajes utilizando **Apache Kafka** y **Spring Boot**. Se ilustra cómo crear y configurar tópicos, producir mensajes y consumirlos, incluyendo el uso de interceptores para procesar o filtrar la información recibida.

![image](https://github.com/user-attachments/assets/eabc7a30-8497-4fe5-bda6-f9a69811a1fc)

---

## Descripción General

- **Productor (Producer)**:
  - Expone un endpoint REST `/producer` para recibir mensajes.
  - Envía dichos mensajes al tópico `topic01` de Kafka.
  - Registra información en el log sobre el envío (offset, partición, etc.).

- **Consumidor (Consumer)**:
  - Configura y escucha el tópico `topic01`.
  - Posee un *interceptor* que detecta si el mensaje contiene la palabra clave `"interceptar"`.
  - Registra en el log la información de los mensajes consumidos.

- **Configuración de Kafka**:
  - Se crea el tópico `topic01` automáticamente con **2 particiones** y **1 réplica**.
  - Se definen fábricas de Productor y Consumidor, especificando los *serializers* y *deserializers* (String).
  - Se utiliza `KafkaProperties` para centralizar los parámetros de conexión a Kafka.

---

## Características Principales

1. **Tópico `topic01`**  
   - 2 particiones  
   - 1 réplica  

2. **Productor**  
   - Usa `KafkaAdmin` para crear el tópico al iniciar la aplicación.  
   - Define un `ProducerFactory` y un `KafkaTemplate` para enviar mensajes.  
   - Endpoint `/producer` (POST) para mandar mensajes.

3. **Consumidor**  
   - Define un `ConsumerFactory` y un `ConcurrentKafkaListenerContainerFactory` para configurar la lectura de mensajes.  
   - Incluye un *RecordInterceptor* que revisa si un mensaje contiene la palabra `"interceptar"` y registra esta acción.  
   - Listeners que consumen el tópico `topic01`, pudiendo repartirse las particiones.

4. **Logs Detallados**  
   - El Productor informa si hubo error o éxito en el envío, mostrando partición y offset.  
   - El Consumidor registra cada mensaje que recibe y si se interceptó la palabra clave.

---

## Estructura de la Configuración

- **`KafkaAdminConfig`**  
  - Se encarga de configurar y crear el tópico `topic01`.

- **`ProducerFactoryConfig`**  
  - Define la fábrica de productores y el `KafkaTemplate` para enviar mensajes.

- **`Consumer01Config`**  
  - Configura el consumidor con deserializadores de tipo String.  
  - Crea el `ConcurrentKafkaListenerContainerFactory` que incluye el interceptor de mensajes.

---

## Estructura de la Configuración

## Uso y Pruebas

En esta sección aprenderás a levantar el entorno de Kafka (mediante Docker Compose) y a probar el flujo completo de envío y recepción de mensajes entre el Productor y el Consumidor.

### 1. Ejecutar Docker Compose

En la raíz del proyecto (o donde tengas tu archivo `docker-compose.yml`), ejecuta:

```bash
docker-compose up -d
```

Este archivo inicia tres contenedores:

- Zookeeper (puerto interno 2181)
- Kafka (puerto mapeado a localhost:9092)
- Kafdrop (interfaz web en localhost:19000 para monitorear Kafka)

Asegúrate de que los contenedores estén en estado “healthy” o corriendo sin errores.

Probar el Envío de Mensajes
a. Envío de Mensajes con Palabra Normal
Usando Postman, cURL o similar, envía un POST al Productor:

```bash
POST http://localhost:8000/producer
Body (JSON):
{
  "message": "Mensaje del productor para los que quieran escucharlo"
}
Si todo funciona, el Productor enviará el mensaje a topic01 y se reflejará en los logs del Consumidor.
```

b. Envío de Mensajes con Palabra Interceptar
El Consumidor cuenta con un interceptor que revisa si la palabra "interceptar" aparece en el texto. Envía un mensaje que la contenga, por ejemplo:

```bash
POST http://localhost:8000/producer
Body (JSON):
{
  "message": "Mensaje con la palabra interceptar"
}
En los logs del Consumidor verás un mensaje indicando que el contenido fue interceptado.
```
