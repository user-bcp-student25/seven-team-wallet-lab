## ADDED Requirements

### Requirement: Clave de idempotencia por intento (HU-04 CA1)
Cada intento de pago SHALL usar una clave de idempotencia única en el ámbito
del pagador, monto, destino y ventana de intento. La duración y política de
retención de la ventana permanecen pendientes de Q-07.

#### Scenario: Asociar una clave al intento
- **DADO** el pagador inicia un intento de pago
- **CUANDO** el intento se envía para procesarse
- **ENTONCES** la solicitud incluye una clave de idempotencia en el ámbito del pagador, monto, destino y ventana de intento
- **Y** la ventana y retención utilizadas corresponden a la decisión aprobada en Q-07

### Requirement: Reproducir el resultado ante una solicitud duplicada (HU-04 CA2)
Una solicitud repetida con la misma clave y el mismo contenido SHALL devolver el
resultado original y SHALL NOT crear una nueva transacción financiera.

#### Scenario: Repetir una solicitud ya procesada
- **DADO** una solicitud de pago ya produjo un resultado para una clave y contenido determinados
- **CUANDO** llega otra solicitud con la misma clave y el mismo contenido
- **ENTONCES** se devuelve el resultado original
- **Y** no se crea una nueva transacción ni un nuevo débito

#### Scenario: Recibir solicitudes concurrentes con la misma clave
- **DADO** dos o más solicitudes concurrentes tienen la misma clave y el mismo contenido
- **CUANDO** se procesan
- **ENTONCES** todas observan el mismo resultado del intento original
- **Y** como máximo se crea una transacción financiera para esa clave

#### Scenario: Recuperar el resultado después de una interrupción
- **DADO** el procesamiento se interrumpe después de registrar la solicitud o su resultado
- **CUANDO** llega un reintento con la misma clave y contenido
- **ENTONCES** el sistema recupera el resultado durable del intento original
- **Y** no crea una segunda transacción financiera
- **Y** los estados intermedios y la recuperación quedan sujetos a la decisión Q-07

### Requirement: Auditar duplicados y distinguir reintentos (HU-04 CA3-4)
El sistema SHALL registrar identificadores de solicitud y correlación para
solicitudes duplicadas y SHALL producir una salida de control que distinga el
intento original de sus reintentos sin contar estos como transacciones
financieras nuevas.

#### Scenario: Registrar una solicitud duplicada
- **DADO** llega una solicitud duplicada con una clave de idempotencia existente
- **CUANDO** se registra el evento de duplicidad
- **ENTONCES** la evidencia contiene el identificador de solicitud y el identificador de correlación
- **Y** no expone secretos ni datos personales innecesarios

#### Scenario: Distinguir intento original y reintento en la salida de control
- **DADO** existe un intento original y una o más solicitudes repetidas
- **CUANDO** se genera la salida de control requerida por HU-04 CA4
- **ENTONCES** identifica el original y cada reintento como tales
- **Y** todas las solicitudes asociadas remiten a una única referencia financiera del resultado original
- **Y** esta salida no implementa conciliación diaria de HU-06, que permanece diferida

