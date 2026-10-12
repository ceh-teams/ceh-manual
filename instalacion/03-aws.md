# Instalación en la nube de Amazon (AWS)

Alternativa a instalar en un equipo propio: el sistema corre como una función de AWS Lambda **en la cuenta de AWS de tu comité**. No hay equipo que mantener y, con el uso de un comité, el gasto queda cerca de cero. **Cada comité usa su propia cuenta: nadie más ve sus datos**, tampoco el proyecto.

> Hace falta un mínimo de perfil técnico. Si no lo tenés, [instalar en un equipo propio](01-instalacion.md) es más simple. **Antes de cargar datos reales**, hacé una prueba completa con datos de demostración (paso 8).

## Qué se crea en tu cuenta

- Un **bucket S3 privado**, cifrado y con versionado, con los datos y las copias. **No se borra si eliminás la pila.**
- Una **tabla de DynamoDB** con el chat, las conversaciones del copiloto y el registro de cambios. **No se borra si eliminás la pila.**
- La **función Lambda** con el programa y una **dirección HTTPS** propia.
- Una **tarea cada 5 minutos** (copias, recordatorios, vencimientos) y una limpieza semanal de registros vencidos.
- Un **presupuesto** con aviso por correo si el gasto se acerca al límite.
- Permisos mínimos: la función solo toca su bucket, su tabla, su clave y su configuración.

No crea redes (VPC), API Gateway, CloudFront ni bases con servidor: son piezas con costo fijo que el sistema no necesita.

## 1. Crear la cuenta de AWS

1. Entrá a <https://portal.aws.amazon.com/billing/signup> y elegí **Crear una cuenta de AWS**.
2. Cargá un correo del comité (no uno personal, para que la cuenta no dependa de una sola persona), una contraseña y el nombre de la cuenta.
3. Completá los datos de contacto y una **tarjeta de crédito o débito internacional**. AWS cobra un monto mínimo de verificación.
4. Verificá el teléfono por SMS o llamada y elegí el plan de soporte **Básico** (gratis).
5. Iniciá sesión en la [consola de AWS](https://console.aws.amazon.com/) como **usuario raíz**.

### Proteger la cuenta (obligatorio)

1. **Activá MFA en el usuario raíz:** consola → tu nombre arriba a la derecha → *Credenciales de seguridad* → *Autenticación multifactor*. [Guía de AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/enable-mfa-for-root.html).
2. **No uses el raíz para el día a día.** Creá un usuario administrador aparte en [IAM](https://console.aws.amazon.com/iam/), también con MFA, y usá ese.
3. **Anotá los accesos** (correo, contraseña, MFA) en un gestor de contraseñas o en papel, **nunca en el sistema ni en un chat**. Que los conozcan al menos dos personas de confianza.

> **El plan gratuito de las cuentas nuevas vence** (según fuentes de terceros, a los 6 meses o al agotar el crédito). Pasado ese plazo hay que pasar la cuenta al plan pago o AWS puede cerrarla. Poné un recordatorio en el calendario.

## 2. Elegir la región

Arriba a la derecha de la consola, elegí una **región de la Unión Europea**, por ejemplo `eu-west-1` (Irlanda). Para la ley argentina de protección de datos la UE es destino adecuado; Estados Unidos y Brasil no. Usá **siempre la misma región** en todos los pasos.

## 3. Descargar la plantilla

Bajá [`ceh.yaml`](https://github.com/ceh-teams/ceh-releases/blob/main/aws/ceh.yaml) (botón *Raw* → guardar como). Es la misma para todas las versiones.

## 4. Anotar los datos de la versión

La plantilla pide tres datos de la versión que vas a instalar:

1. Entrá a la [última versión](https://github.com/ceh-teams/ceh-releases/releases/latest) y abrí `latest.json`.
2. Buscá el bloque con `"os": "lambda"`.
3. Copiá su `url` y su `sha256`.

| Parámetro | De dónde sale |
|---|---|
| **VersionCEH** | El número de versión (por ejemplo `4.4.0`) |
| **CodigoURL** | La `url` del bloque `lambda` (termina en `_lambda_arm64.zip`) |
| **CodigoSHA256** | El `sha256` del bloque `lambda` |

Si la huella no coincide con la del paquete, la instalación se cancela y no queda nada.

## 5. Crear la pila en CloudFormation

1. Abrí [CloudFormation → Crear pila](https://console.aws.amazon.com/cloudformation/home#/stacks/create) (verificá que sigas en la región de la UE).
2. Elegí **Con recursos nuevos (estándar)** → **Cargar un archivo de plantilla** → subí `ceh.yaml` → *Siguiente*.
3. Poné un **nombre de pila** (por ejemplo `ceh`) y completá los parámetros:

| Parámetro | Qué poner |
|---|---|
| **VersionCEH**, **CodigoURL**, **CodigoSHA256** | Los del paso 4 |
| **CorreoAlertas** | Correo al que avisa el presupuesto |
| **PresupuestoUSD** | Dejá `1` |
| **ModeloBedrock** | Dejalo **vacío**. El copiloto se activa después desde el propio sistema |
| **RegionBedrock** | Dejá `eu-central-1` |
| **ConcurrenciaMaxima** | Dejá `0`. Si AWS lo permite, un valor chico (por ejemplo `10`) pone un techo al gasto si alguien martilla la dirección |

4. *Siguiente* hasta la última pantalla. **Marcá la casilla que reconoce que la plantilla crea roles de IAM** y apretá **Enviar**.
5. Esperá unos minutos hasta que el estado diga **`CREATE_COMPLETE`** (se actualiza con el botón de recarga).

Si termina en `ROLLBACK_COMPLETE`, abrí la pestaña **Eventos** de la pila: el primer error en rojo dice qué falló (huella que no coincide, enlace mal copiado, región distinta…). Borrá la pila fallida y volvé a crearla.

## 6. Abrir el sistema

1. En la pestaña **Salidas** de la pila copiá `DireccionDelSistema` y `TokenInstalacion`.
2. Abrí la dirección agregándole `instalar?token=` y el valor del token:

   ```
   https://…lambda-url…/instalar?token=<TokenInstalacion>
   ```

3. Aparece el asistente. El código es **de un solo uso**: tratalo como una clave y no lo compartas.

Requiere la versión 4.4.0 o posterior (las anteriores pueden rechazar el asistente con «Pedido rechazado por la protección CSRF»).

## 7. Completar el asistente

Seguí el [primer arranque](02-primer-arranque.md) (nombre del comité, frase maestra, administrador). En la nube, **guardá en papel, en dos lugares distintos, la frase maestra y el código de recuperación**: son la única forma de sacar tus datos de AWS o recuperarlos si algo falla, y nadie del proyecto puede ayudarte sin ellos.

Diferencia con el equipo propio: el sistema **se abre solo en cada arranque**, sin pedir la frase. Para eso guarda la clave de apertura en un **parámetro cifrado de SSM** de tu cuenta (`/ceh/<nombre de la pila>/boveda`). **Quien tenga permiso de AWS para leerlo puede descifrar todo**, por eso la cuenta se protege con MFA, no se comparten credenciales y conviene activar [CloudTrail](https://console.aws.amazon.com/cloudtrail/) (los eventos de administración son gratis).

Después del asistente, activá las opciones que quieras en *Configuración del sistema → Integraciones* (correo, copiloto, búsqueda en internet). Dónde conseguir cada clave: [guía](https://github.com/ceh-teams/ceh-releases/blob/main/aws/README.md#5-activá-las-opciones-si-querés).

## 8. Primera prueba

Antes de cargar datos reales, en una **cuenta de prueba**: creá la pila, instalá, cargá un par de registros, abrí el sistema desde dos navegadores a la vez, usá el chat, generá una copia, actualizá y volvé atrás. Revisá en [Budgets](https://console.aws.amazon.com/billing/home#/budgets) y [Cost Explorer](https://console.aws.amazon.com/cost-management/home#/cost-explorer) que el gasto sea el esperado.

## Costos

Estimación con el uso de un comité. **Verificá los precios en la página de AWS.**

| Pieza | Costo esperado |
|---|---|
| Lambda | Dentro del nivel gratuito permanente |
| S3 (base, archivos, copias) | Centavos por mes |
| DynamoDB | Dentro del nivel gratuito permanente, según fuentes de terceros |
| Copiloto y buscadores (opcionales) | Se pagan a Scaleway y a cada buscador; es lo único que crece con el uso |
| SSM, Presupuesto | Gratis |
| CloudWatch Logs (7 días) | Centavos o gratis |

Puede subir el gasto: muchísimos pedidos (usá **ConcurrenciaMaxima** y la alarma del presupuesto) y adjuntos o copias muy grandes.

## Límites que conviene conocer

- **Adjuntos:** una foto o archivo de más de unos **4 MB** no se puede subir.
- **Restaurar una copia grande** por el navegador no funciona: subí el `.cehbk` al bucket, carpeta `copias/`, y restauralo desde *Mantenimiento → Restaurar* («…o una copia guardada»).
- **Recordatorios:** pueden llegar hasta 5 minutos tarde.
- **Arranque en frío:** el primer pedido después de un rato sin uso tarda unos segundos.

## Copias de seguridad

Se guardan **en el bucket** (`copias/`). Descargá una copia a tu equipo de vez en cuando y guardala en otro lugar: **el bucket y la cuenta son un único punto de falla.** El bucket tiene versionado (30 días).

## Actualizar

**Con un clic:** *Mantenimiento → Actualizaciones*. El sistema avisa si hay versión nueva (lee un archivo público firmado, sin enviar datos de tu comité) y ofrece **Actualizar**: baja el paquete, verifica firma y huella, guarda un punto de restauración y cambia de versión. **Volver atrás** deja código y datos como estaban al actualizar (se pierde lo cargado desde entonces). Opcionalmente se activa la actualización automática (apagada por defecto) con ventana horaria.

**Cuando hay que actualizar la plantilla** (si una versión lo pide, el botón no aparece):

1. Bajá la [`ceh.yaml`](https://github.com/ceh-teams/ceh-releases/blob/main/aws/ceh.yaml) actual.
2. En CloudFormation: tu pila → **Actualizar → Reemplazar plantilla actual** → subila.
3. En **VersionCEH**, **CodigoURL** y **CodigoSHA256** poné los de la versión **instalada o una posterior** (si no, la función vuelve a la versión de los parámetros).
4. Revisá los cambios que muestra CloudFormation y confirmá.

## Desinstalar

Eliminar la pila **no borra** el bucket ni la tabla (a propósito, para no perder datos). Antes de borrar nada, **descargá una copia y verificá que se abre**. Para eliminar todo, vaciá y borrá el bucket y la tabla a mano desde S3 y DynamoDB. Es irreversible: confirmalo con el comité.
