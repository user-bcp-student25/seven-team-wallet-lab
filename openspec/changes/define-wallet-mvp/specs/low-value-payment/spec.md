## ADDED Requirements

### Requirement: Confirmación previa y límites de pago (HU-02 CA1; reglas de negocio)
La experiencia de pago SHALL mostrar el destinatario y el monto antes de la
confirmación final. El rango global documentado es de PEN 1 a PEN 2,000; los
límites adicionales por proveedor o canal siguen sujetos a Q-04.

#### Scenario: Revisar el destinatario y el monto antes de confirmar
- **DADO** el pagador prepara una transferencia con destinatario y monto
- **CUANDO** llega al paso previo a la confirmación final
- **ENTONCES** la pantalla muestra la identidad del destino y el monto de la transferencia
- **Y** el pago todavía no se presenta como confirmado

#### Scenario: Aplicar el rango global de montos
- **DADO** el pagador ingresa un monto expresado en soles
- **CUANDO** el monto es menor que PEN 1 o mayor que PEN 2,000
- **ENTONCES** el sistema impide enviar ese intento de pago
- **Y** informa que el monto está fuera del rango permitido

#### Scenario: Validar los límites globales inclusivos
- **DADO** el monto ingresado es PEN 1 o PEN 2,000
- **CUANDO** se aplica la validación del rango global de la wallet
- **ENTONCES** el monto no se rechaza por estar fuera de ese rango global
- **Y** cualquier límite más restrictivo del proveedor o canal se aplica solo cuando se resuelva Q-04

### Requirement: Fondos y resultado terminal (HU-02 CA2 y CA4; reglas de negocio)
El pago SHALL requerir saldo disponible suficiente y entregar un único resultado
terminal por intento. Un timeout o resultado incierto SHALL NOT tratarse como
fallo, expiración ni autorización para crear otro débito o cambiar de riel.

#### Scenario: Rechazar un pago sin saldo suficiente
- **DADO** el saldo disponible del pagador es menor que el monto solicitado
- **CUANDO** intenta confirmar el pago
- **ENTONCES** el pago no se ejecuta
- **Y** se muestra "Fondos insuficientes"

#### Scenario: Entregar un solo resultado terminal
- **DADO** el intento de pago alcanza un resultado autoritativo definido por la máquina de estados aprobada en Q-05
- **CUANDO** el resultado se comunica al pagador
- **ENTONCES** el intento recibe un único resultado terminal: exitoso, fallido o expirado
- **Y** no se emite un segundo resultado terminal para ese intento

#### Scenario: Mantener incierto un intento sin resultado autoritativo
- **DADO** vence una espera o se produce un timeout sin resultado autoritativo del pago
- **CUANDO** el sistema informa el estado del intento
- **ENTONCES** no lo declara fallido ni expirado por causa del timeout
- **Y** no crea otro débito ni cambia a otro riel mientras el resultado original siga incierto
- **Y** la presentación de ese estado queda sujeta a la definición de estados de Q-05

#### Scenario: Ofrecer una acción después de un fallo o expiración confirmados
- **DADO** el pago tiene un resultado terminal autoritativo fallido o expirado
- **CUANDO** el pagador consulta el resultado
- **ENTONCES** recibe una acción clara para volver o reintentar
- **Y** un reintento no crea un cobro duplicado
- **Y** la semántica del nuevo intento respeta la política idempotente de HU-04 y Q-07

### Requirement: Persistencia e historial del pago (HU-02 CA3; reglas de negocio)
Un pago exitoso SHALL tener una referencia visible y persistirse en el historial.
La vista "Mis movimientos" SHALL mostrar los 10 movimientos más recientes y
permitir consultar incrementalmente los movimientos de los últimos 90 días.

#### Scenario: Persistir un pago exitoso
- **DADO** el resultado autoritativo del intento es exitoso
- **CUANDO** se muestra la confirmación al pagador
- **ENTONCES** se muestra una referencia de transacción
- **Y** el pago queda persistido en el historial
- **Y** el saldo disponible se actualiza según el resultado confirmado

#### Scenario: Mostrar los movimientos recientes
- **DADO** el pagador abre "Mis movimientos"
- **CUANDO** existen movimientos disponibles
- **ENTONCES** se muestran primero los 10 movimientos más recientes

#### Scenario: Consultar movimientos anteriores dentro de 90 días
- **DADO** existen más de 10 movimientos dentro de los últimos 90 días
- **CUANDO** el pagador solicita "Ver más movimientos" o continúa desplazándose
- **ENTONCES** se cargan movimientos adicionales de forma incremental
- **Y** no se incluyen movimientos anteriores al periodo definido

### Requirement: Medición del checkout y activación (HU-02 CA5-6)
El flujo SHALL producir evidencia para medir abandono de checkout y reconocer la
activación del pagador, conforme a las definiciones aprobadas en Q-03 y Q-14.

#### Scenario: Reportar la tasa de abandono de checkout
- **DADO** Q-14 define la ventana de medición y Q-03 define los eventos y denominadores
- **CUANDO** se genera el reporte para la cohorte y periodo aprobados
- **ENTONCES** el reporte expone la tasa observada y permite compararla con la línea base de 30% y el objetivo de 8%
- **Y** no afirma que el objetivo se alcanzó sin evidencia observada

#### Scenario: Emitir el evento de activación del pagador
- **DADO** una transacción exitosa cumple la definición de activación aprobada en Q-03
- **CUANDO** se confirma el resultado exitoso
- **ENTONCES** se emite la evidencia del evento de activación para el reporte de MAU
- **Y** su deduplicación y atribución siguen la definición aprobada

### Requirement: Cargo ITF indicado por las reglas de negocio (alcance condicionado a Q-19)
Cuando la regla aprobada determine que corresponde aplicar ITF, el pago SHALL
mostrar el cargo como un movimiento adicional con la glosa ITF. La regla fuente
indica una tasa de 0.05% para montos iguales o mayores a PEN 1,000; la
aplicabilidad, base de cálculo, redondeo y tratamiento contable requieren la
decisión Q-19 y no se consideran cerrados por esta propuesta.

#### Scenario: Mostrar el movimiento ITF cuando la regla aprobada lo aplique
- **DADO** un pago exitoso es de PEN 1,000 o más y la decisión aprobada de Q-19 determina que corresponde ITF
- **CUANDO** se presenta el historial del pago
- **ENTONCES** se muestra un movimiento adicional con la glosa "ITF"
- **Y** el importe se calcula con la base y el redondeo aprobados

