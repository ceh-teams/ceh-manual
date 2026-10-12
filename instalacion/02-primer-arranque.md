# Primer arranque y kit de recuperación

Después de [instalar](01-instalacion.md), abrí el enlace que mostró el instalador (`http://127.0.0.1:8080/instalar?token=…`). El código del enlace evita que otra persona de la red complete la instalación antes que vos. Si lo perdiste, está en el archivo `instalar.url` de la carpeta de datos, que se borra solo al terminar.

## El asistente

Es una sola pantalla:

| Campo | Qué poner |
|---|---|
| **Nombre del comité** | Hasta 80 caracteres. |
| **Frase maestra** (dos veces) | Mínimo **16 caracteres**. Mejor varias palabras, fáciles de recordar para vos y difíciles de adivinar. Es la llave de todos los datos. |
| **Usuario administrador** | El nombre con el que vas a entrar. |
| **Contraseña del administrador** (dos veces) | Mínimo 12 caracteres con mayúscula, minúscula, número y símbolo, sin el usuario adentro. **No puede ser igual a la frase maestra.** |
| **Carpeta de copias** | Donde se guardan las copias de seguridad. Idealmente **otro disco**, no el de los datos. El asistente prueba que se pueda escribir. |

### Frase maestra y contraseña no son lo mismo

- **La contraseña** es para entrar todos los días; cada persona tiene la suya.
- **La frase maestra** abre la bóveda donde está la clave que cifra los datos. Se escribe **una vez cada vez que el equipo se reinicia** y para restaurar copias. La conocen pocas personas de confianza (por ejemplo, quien preside y quien administra).

> **La frase no se guarda en ningún lado.** Si se pierde y tampoco tenés el código de recuperación, **los datos no se pueden recuperar**. No existe «olvidé mi frase» y nadie del proyecto puede ayudar.

### ¿Venís de otro equipo?

Al pie de la misma pantalla, **«¿Ya tenés una copia de seguridad? Restaurarla en este equipo»** (archivo `.cehbk`) permite pasar el sistema a otro equipo en vez de crear un comité nuevo.

## El kit de recuperación

Al terminar se muestra **una sola vez** un **código de recuperación** con este formato (ejemplo): `ABCD-EFGH-IJKL-MNOP-QRST-UVWX`. Es una segunda llave de la bóveda: sirve para desbloquear el sistema o abrir una copia si se pierde la frase. Vale tanto como la frase.

En esa pantalla:

1. **Descargá el kit** (`CEH-Manager-kit-de-recuperacion.txt`) o anotalo a mano.
2. **Imprimilo** y guardalo en un lugar seguro y **físico**: caja fuerte, sobre cerrado en el archivo del comité.
3. **No lo guardes** en el mismo equipo, ni en un chat, ni en un correo.
4. Marcá que lo guardaste y escribí **el último grupo** del código (`UVWX` en el ejemplo) para comprobar que lo anotaste bien.

Hasta que confirmes, el sistema muestra un aviso de **kit pendiente**. Si cerraste la pantalla, el administrador puede generar un kit nuevo mientras siga pendiente.

### Usar el código de recuperación

- Para desbloquear: en `/desbloquear`, elegí **código de recuperación**, o por terminal `ceh unlock --codigo`.
- Para copias por terminal: `ceh backup --codigo` y `ceh restore --codigo archivo.cehbk`.

### Cambiarlos más adelante

En **Configuración del sistema → Copias de seguridad** el administrador puede **cambiar la frase maestra** (instantáneo; las copias viejas siguen abriéndose con la frase que tenían) y **generar un kit nuevo** (el código viejo deja de servir). Ambos piden la frase actual. Hacelo cuando alguien que conocía la frase deja el comité.

## Después del asistente

1. **Ingresá** con el usuario administrador y aceptá las condiciones de uso y la política de privacidad.
2. **Activá la verificación en dos pasos** en *Mi cuenta → Verificación en dos pasos* (aplicación de códigos) y, si querés, huella o llave de acceso en *Biometría*.
3. **Creá las cuentas del resto del comité** (*Configuración del sistema → Miembros y ayudantes*, [ver manual](../04-comite.md)). Cada una queda con un **rol** que define qué ve y qué puede hacer.
4. **Revisá las copias de seguridad:** que las copias automáticas estén andando (*Configuración del sistema → Copias de seguridad*; por defecto, cada 24 horas y 30 días de retención).
5. **Completá los datos legales** en *Textos legales* y el [registro ante la AAIP](https://github.com/ceh-teams/ceh-releases#inscripción-ante-la-aaip).
6. Si querés correo, copiloto o búsqueda en internet, configurá *Integraciones* (dónde sacar las claves: [guía](https://github.com/ceh-teams/ceh-releases/blob/main/aws/README.md#5-activá-las-opciones-si-querés)).
7. Para empezar a cargar datos, seguí con el [manual de usuario](../01-primeros-pasos.md).

## Lista de control del primer día

- [ ] Frase maestra anotada y guardada por al menos dos personas de confianza.
- [ ] Código de recuperación impreso, en lugar seguro y confirmado en pantalla.
- [ ] Verificación en dos pasos activada en la cuenta del administrador.
- [ ] Una copia manual generada y **probada** en otro equipo: una copia que nunca se probó restaurar no es una copia.
- [ ] Cifrado de disco activo (`ceh doctor`).
- [ ] Cuentas del comité creadas, cada una con su rol.
