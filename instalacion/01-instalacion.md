# Instalación

Para quien instala CEH Manager por primera vez, sin conocimientos técnicos. Cuando termines, seguí con el [primer arranque](02-primer-arranque.md).

CEH Manager es **un solo programa**. No necesita instalar bases de datos, Docker ni Python. Solo hace falta un equipo que quede **prendido** (una PC, una Mac o una mini PC), de 64 bits y con el **disco cifrado**. Si preferís no tener equipo, existe la opción de la [nube de Amazon](https://github.com/ceh-teams/ceh-releases/blob/main/aws/README.md).

## Qué elegir

| Quiero… | Hacé esto |
|---|---|
| Ver cómo es con datos inventados | [Versión demo](#versión-demo) |
| Probarlo un rato, sin instalar | [Modo prueba](#modo-prueba-sin-instalar) |
| Que quede funcionando siempre y arranque con el equipo | Instalar como servicio: [macOS](#macos), [Windows](#windows), [Linux](#linux) |

## 1. Descargar

Entrá a [la última versión](https://github.com/ceh-teams/ceh-releases/releases/latest) y bajá el archivo de tu equipo (`<versión>` es el número, por ejemplo `4.4.0`):

| Tu equipo | Archivo |
|---|---|
| Mac (instalador, Intel y Apple Silicon) | `ceh-manager-<versión>.pkg` |
| Mac Apple Silicon (M1, M2…) | `ceh-manager_<versión>_darwin_arm64.tar.gz` |
| Mac Intel | `ceh-manager_<versión>_darwin_amd64.tar.gz` |
| Windows (instalador) | `ceh-manager-<versión>-x64.msi` (ARM: `-arm64.msi`) |
| Windows sin instalador | `ceh-manager_<versión>_windows_amd64.zip` |
| Linux Debian / Ubuntu | `ceh-manager_<versión>_amd64.deb` (o `_arm64`) |
| Linux Fedora / RHEL | `ceh-manager-<versión>-1.x86_64.rpm` (o `.aarch64`) |
| Otro Linux | `ceh-manager_<versión>_linux_amd64.tar.gz` (o `_arm64`) |

¿No sabés qué procesador tiene tu Mac? Menú  → *Acerca de esta Mac*.

Los instaladores `.pkg` y `.msi` aparecen un rato después de publicada la versión. Si todavía no están, usá el archivo comprimido: hace lo mismo.

### Verificar la descarga

Cada versión trae `ceh-manager_<versión>_checksums.txt` con la huella SHA-256 de cada archivo (los instaladores, en su propio `.sha256`). Comprobá que coincida:

- macOS / Linux: `shasum -a 256 archivo`
- Windows (PowerShell): `Get-FileHash archivo -Algorithm SHA256`

Si no coincide, descartá el archivo y bajalo de nuevo.

## 2. Activar el cifrado de disco

Los datos de personas ya van cifrados dentro del sistema, pero el disco también debe estarlo para proteger adjuntos y copias si roban el equipo.

| Sistema | Cómo |
|---|---|
| macOS | Ajustes del Sistema → Privacidad y seguridad → **FileVault** |
| Windows | Configuración → Privacidad y seguridad → **Cifrado de dispositivo** (o BitLocker en versiones Pro) |
| Linux | Se elige al instalar el sistema (**LUKS**) |

El comando `ceh doctor` avisa si falta. Es un aviso, no frena la instalación.

## Versión demo

Datos inventados, solo en tu computadora y **nunca con datos reales**.

1. Descomprimí el archivo `.tar.gz` o `.zip`.
2. En una terminal, dentro de esa carpeta (en Windows, `ceh.exe`):

   ```
   ./ceh serve --demo --data ./demo-datos
   ```

3. Abrí el enlace de instalación que muestra la terminal y completá el asistente ([primer arranque](02-primer-arranque.md)).
4. En **otra terminal**: `./ceh seed-demo --data ./demo-datos`
5. Entrá a <http://127.0.0.1:8081/demo>: lista las cuentas de ejemplo.

Usá siempre una carpeta aparte (`--data ./demo-datos`). Esa página solo existe en el puerto local `8081`: no se ve desde otra máquina.

## Modo prueba (sin instalar)

1. Descomprimí el `.tar.gz` o `.zip`.
2. macOS / Linux: abrí una terminal en esa carpeta y escribí `./ceh`. Windows: doble clic en `ceh.exe`.
3. En la ventana aparece un enlace parecido a `http://127.0.0.1:8080/instalar?token=…`. Abrilo en el navegador.
4. Seguí el [primer arranque](02-primer-arranque.md).

Los datos quedan en tu carpeta de usuario. **Si cerrás la ventana, el sistema se detiene.**

## Instalar como servicio

El sistema queda funcionando siempre y arranca con el equipo. Solo escucha en el propio equipo (`127.0.0.1:8080`). Al terminar, el instalador muestra un **enlace** para completar el asistente en el navegador.

### macOS

**Con el instalador (`.pkg`):** doble clic y seguí los pasos (pide la clave de la Mac). Al terminar se abre el navegador con el asistente. El `.pkg` todavía no está firmado por Apple: si macOS lo bloquea, *Ajustes del Sistema → Privacidad y seguridad* → **Abrir igualmente**.

**Con el archivo comprimido:** descomprimí, abrí la Terminal en esa carpeta y corré:

```
sudo ./ceh install
```

### Windows

**Con el instalador (`.msi`):** doble clic (pide permiso de administrador). Queda como servicio de Windows. El enlace con el código está en `C:\ProgramData\CEH Manager\instalar.url`. Si Windows dice «Windows protegió su PC»: *Más información* → *Ejecutar de todas formas*.

**Con el `.zip`:** descomprimí, abrí *Símbolo del sistema* **como administrador**, andá a la carpeta y corré:

```
ceh.exe install
```

### Linux

Con paquete (recomendado). **No uses `ceh install` en este caso:**

```
sudo apt install ./ceh-manager_<versión>_amd64.deb      # Debian, Ubuntu
sudo dnf install ./ceh-manager-<versión>-1.x86_64.rpm   # Fedora, RHEL
sudo cat /var/lib/ceh-manager/instalar.url               # para volver a ver el enlace
```

Con el `.tar.gz`:

```
tar xzf ceh-manager_<versión>_linux_amd64.tar.gz
sudo ./ceh install
```

### Dónde queda cada cosa

| Cosa | macOS | Windows | Linux (paquete) |
|---|---|---|---|
| Programa | `/usr/local/bin/ceh` | `C:\Program Files\CEH Manager\ceh.exe` | `/usr/bin/ceh` |
| Datos | `/Library/Application Support/CEH Manager` | `C:\ProgramData\CEH Manager` | `/var/lib/ceh-manager` |
| Registro (log) | `<datos>/logs/ceh.log` | `<datos>\logs\ceh.log` | `journalctl -u ceh-manager` |

Opciones de `ceh install` (todas opcionales): `--addr 127.0.0.1:9000` (otro puerto), `--data <carpeta>` (otra carpeta de datos), `--https`, `--proxy-confiable <IPs>` y `--usuario <cuenta>` (macOS/Linux).

## Qué pasa al reiniciar el equipo

**Cada vez que el equipo arranca, el sistema queda bloqueado** hasta que alguien escribe la **frase maestra**. Es a propósito: la frase no se guarda en el equipo, así que quien se lleve el equipo apagado no puede leer los datos. Para desbloquear:

- En el navegador: `http://127.0.0.1:8080/desbloquear`.
- Por terminal, en el equipo: `ceh unlock --url http://127.0.0.1:8080` (pide la frase sin mostrarla).
- Si olvidaste la frase, usá el **código de recuperación** ([primer arranque](02-primer-arranque.md#el-kit-de-recuperación)).

Mientras el sistema está bloqueado **no se hacen copias de seguridad**.

## Actualizar

1. **Generá una copia de seguridad** (*Mantenimiento → Generar copia ahora*) y confirmá que se guardó.
2. Bajá la versión nueva y verificá su huella.
3. Instalala **encima de la anterior**, con el mismo instalador o comando. Los datos no se tocan.
4. Desbloqueá el sistema con la frase maestra.

Para saber si hay una versión nueva sin enviar datos de tu comité: `ceh update check` (avisa) y `ceh update download` (baja y verifica la firma).

## Si algo falla

| Síntoma | Qué hacer |
|---|---|
| macOS: «no se puede abrir porque no es de un desarrollador identificado» | Clic derecho sobre el programa → **Abrir** → **Abrir** |
| Windows: «Windows protegió su PC» | *Más información* → *Ejecutar de todas formas* |
| No encuentro el enlace de instalación | Mirá la terminal, o `instalar.url` en la carpeta de datos (sección «Dónde queda cada cosa») |
| El enlace no abre | El servicio no arrancó. Revisá el registro (log) de la tabla de arriba |
| Pide la frase y no la tengo | Usá el código de recuperación del kit |

Si sigue sin andar, [abrí un issue](https://github.com/ceh-teams/ceh-releases/issues/new) con la versión (`ceh version`), tu sistema operativo y el mensaje de error. **Sin datos reales, frases ni claves**: el repositorio es público.
