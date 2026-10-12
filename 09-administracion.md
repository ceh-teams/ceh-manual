# 9. Administración

Solo para cuentas con permisos de administración (menú **Configuración del sistema**).

## Roles y permisos

En **Roles** se define qué ve y edita cada rol por módulo (incluidos «Miembros del CEH», «GVP» y «Ayudantes»). Una cuenta tiene un rol y puede además marcarse **Administrador** (acceso total); sin rol tiene que ser administradora.

## Integraciones

**Configuración del sistema → Integraciones.** Todo es opcional; sin claves, esas funciones no existen y no sale nada a internet. Las claves se guardan **cifradas** y no vuelven a mostrarse.

- **Correo**: para los avisos.
- **Copiloto (IA)**: URL del proyecto y clave de API de Scaleway. **Probar la conexión** comprueba que funcione.
- **Copiloto: quién lo usa**: la clave del comité la usan todas las cuentas con Chat o solo las marcadas; y si cada persona puede cargar la suya.
- **Búsqueda en internet**: claves de Exa, Linkup, Brave Search o Tavily (todos con plan gratuito) y el orden en que se prueban; si el primero no tiene cuota, se usa el siguiente. **Probar** gasta un crédito.

Dónde conseguir cada clave: [guía de AWS e integraciones](https://github.com/ceh-teams/ceh-releases/blob/main/aws/README.md#5-activá-las-opciones-si-querés).

## Textos legales

**Configuración del sistema → Textos legales**: condiciones de uso y política de privacidad (admiten títulos con `#`, listas, negrita y cursiva). Ahí también se cargan los datos del responsable que pide la **Ley 25.326**: contacto para ejercer derechos, domicilio, número de inscripción en la AAIP y dónde se guardan los datos. «Restablecer textos» vuelve al modelo y conserva esos datos. **Conviene revisarlos con un abogado.**

- Los formularios públicos piden aceptar la política y guardan cuándo y qué versión.
- Cada cuenta nueva los acepta en su primer ingreso.

## Copias de seguridad y recuperación

**Configuración del sistema → Copias de seguridad:**

- **Generar copia ahora** (y las automáticas). Guardalas también en otro lugar.
- **Cambiar la frase maestra**: instantáneo. Hacelo cuando alguien que la conocía deja el comité.
- **Generar un kit de recuperación nuevo**: el anterior deja de servir.

**Sin la frase maestra ni el código de recuperación los datos no se pueden recuperar.** Guardalos en papel, en lugares distintos.

## Actualizaciones

**Mantenimiento → Actualizaciones**: avisa si hay versión nueva (firmada), con **Actualizar** y **Volver atrás** en la nube, y actualización automática opcional. Detalles en [ceh-releases](https://github.com/ceh-teams/ceh-releases#actualizar).
