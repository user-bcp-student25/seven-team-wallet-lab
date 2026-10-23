## ADDED Requirements

### Requirement: Guided and validated onboarding (HU-01 CA1-3; business rules)
The onboarding flow SHALL show its required steps and current step, validate the
registration data, prevent completion when required data is missing or invalid,
and activate the wallet only after the approved onboarding checks are complete.

#### Scenario: Mostrar pasos y paso actual
- **DADO** una persona inicia el onboarding desde un smartphone
- **CUANDO** abre el flujo y avanza entre pasos
- **ENTONCES** la interfaz muestra la secuencia requerida y el paso actual en todo momento

#### Scenario: Bloquear datos obligatorios o inválidos
- **DADO** la persona deja un dato obligatorio vacío o ingresa un dato que incumple una regla de validación aprobada
- **CUANDO** intenta avanzar o registrar la cuenta
- **ENTONCES** el flujo impide completar el paso y muestra un mensaje accionable para corregirlo

#### Scenario: Validar los campos descritos por las reglas de negocio
- **DADO** la persona completa tipo de documento DNI, número de DNI, celular, correo y contraseña
- **CUANDO** el flujo valida los datos
- **ENTONCES** el número de DNI acepta solo dígitos y hasta 8 caracteres
- **Y** el celular acepta solo dígitos del 0 al 9 y hasta 9 caracteres
- **Y** la contraseña acepta caracteres alfanuméricos y hasta 20 caracteres
- **Y** el correo debe cumplir la regla de dominio válido y el formato aclarado por Q-20
- **Y** no se inventan longitudes mínimas ni reglas de formato adicionales

#### Scenario: Rechazar un cliente ya existente
- **DADO** ya existe el cliente correspondiente a los datos de registro
- **CUANDO** la persona intenta registrar la cuenta
- **ENTONCES** el alta no crea un segundo cliente
- **Y** muestra el mensaje "El cliente ya existe"

#### Scenario: Activar la wallet al completar el onboarding
- **DADO** se completaron los pasos requeridos y las verificaciones simuladas que hayan sido aprobadas para el MVP
- **CUANDO** el alta finaliza correctamente
- **ENTONCES** la wallet cambia al estado activo
- **Y** se muestra "El cliente fue creado con éxito"
- **Y** se muestra Home del Wallet
- **Y** la persona puede acceder al punto de entrada de pagos de bajo monto

### Requirement: Instrumentación de onboarding (HU-01 CA4-6)
El sistema SHALL producir los eventos y datos necesarios para evaluar el
onboarding y los objetivos de adquisición, sin afirmar que un objetivo de
negocio se alcanzó por el solo hecho de generar un reporte.

#### Scenario: Registrar el inicio, los pasos y el cierre
- **DADO** la taxonomía y las fronteras de medición requeridas por Q-02 están aprobadas
- **CUANDO** una persona inicia el onboarding, completa pasos o cierra el flujo
- **ENTONCES** se registran los eventos correspondientes para esa secuencia
- **Y** la evidencia permite calcular la duración usando las fronteras aprobadas

#### Scenario: Reportar la duración de onboarding
- **DADO** existen datos de una cohorte y Q-02 define el inicio, el fin y el tratamiento de pausas
- **CUANDO** se genera el reporte del periodo acordado
- **ENTONCES** el reporte expone la mediana de duración y permite compararla con la línea base de 8 minutos y el objetivo de 3 minutos
- **Y** el reporte no declara alcanzado el objetivo sin datos que lo demuestren

#### Scenario: Reportar nuevos usuarios activos y adquisición
- **DADO** Q-03 define usuario activo, deduplicación, atribución de gasto y fórmula CAC
- **CUANDO** se genera el reporte mensual para los primeros tres meses
- **ENTONCES** incluye conteos de nuevos usuarios activos y gasto de adquisición atribuible
- **Y** permite contrastar los objetivos de MAU de 1,000 a 2,000 y CAC de PEN 10
- **Y** no afirma que se alcanzaron esos objetivos sin evidencia de la cohorte

