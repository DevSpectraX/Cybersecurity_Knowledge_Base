---
tags: [red_team, recon, osint, cheatsheet, comandos]
---


Nota de consulta rápida para ejecución de comandos *one-liners* durante la fase de reconocimiento.

---

## 🔍 1. Subdominio & DNS (Pasivo y Activo)

### Subfinder (Pasivo)
```bash
# Enumeración pasiva simple
subfinder -d <DOMINIO> -o subdominios.txt

# Subfinder con salida silenciosa (útil para pipe)
subfinder -d <DOMINIO> -silent
````

### OWASP Amass (Pasivo / Activo)


```Bash
# Enumeración pasiva
amass enum -passive -d <DOMINIO> -o amass_pasivo.txt

# Enumeración activa con resolución de nombres
amass enum -active -d <DOMINIO> -p 80,443 -o amass_activo.txt
```

### Transferencia de Zona DNS (AXFR)


```Bash
# Probar transferencia de zona con dig
dig axfr @<IP_DNS_SERVER> <DOMINIO>

# Probar con fiercer/host
host -l <DOMINIO> <IP_DNS_SERVER>
```

### Fuerza Bruta DNS (puredns / dnsrecon)


```Bash
# Resolución por fuerza bruta con puredns
puredns bruteforce /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt <DOMINIO> -r resolvers.txt
```

## 🌐 2. Descubrimiento HTTP/HTTPS & Fingerprinting

### httpx (Filtro de URLs vivas)

```Bash
# Filtrar dominios activos y extraer códigos de estado, tecnologías e IPs
cat subdominios.txt | httpx -title -tech-detect -status-code -ip -o objetivos_vivos.txt

# Filtrar solo respuestas 200 OK y 302 Redirect
cat subdominios.txt | httpx -mc 200,302 -silent
```

### Fuzzing de directorios (ffuf)


```Bash
# Fuzzing básico de rutas web
ffuf -u https://<DOMINIO>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -mc 200,301,302

# Fuzzing filtrando por tamaño de respuesta para evitar falsos positivos
ffuf -u https://<DOMINIO>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt -fs <TAMANO_A_EXCLUIR>
```

## 📡 3. Escaneo de Puertos y Servicios (Nmap)

### Escaneo Silencioso SYN & Detección de Versiones

```Bash
# Escaneo de puertos top 1000
nmap -sS -sV -Pn -p- --min-rate 1000 -iL objetivos_vivos.txt -oA escaneo_completo

# Escaneo de puertos específicos sin ping
nmap -sS -sV -Pn -p 80,443,8080,8443,22,3389 <IP_OBJETIVO>
```

## 👤 4. Perfilado de Identidad y Metadatos

### ExifTool (Análisis y Extracción de Metadatos)


```Bash
# Extraer metadatos de un archivo específico
exiftool documento.pdf

# Extraer creador y autor de todos los PDFs del directorio
exiftool -ext pdf -Author -Creator -Title .

# Eliminar todos los metadatos de un archivo
exiftool -all= documento.pdf
```

### pyMeta (Descarga y Extracción Masiva)


```Bash
# Buscar y descargar documentos de un dominio para extraer metadatos
pymeta -d <DOMINIO> -m 50 -o metadatos_out
```

### Crosslinked (Scraping de Nombres para Phishing)


```Bash
# Generar lista de correos desde perfiles de LinkedIn
crosslinked -f '{first}.{last}@<DOMINIO>' "<NOMBRE_EMPRESA>" -o usuarios_linkedin.txt
```

### o365creeper (Validación de Correos en M365)

```Bash
# Validar usuarios contra Microsoft 365 sin login fallido
python3 o365creeper.py -f usuarios_linkedin.txt -o usuarios_validos_m365.txt
```

### Kerbrute (Password Spraying / User Enum en Kerberos)


```Bash
# Enumeración de usuarios válidos en AD vía Kerberos
kerbrute userenum -d <DOMINIO_LOCAL> --dc <IP_DC> usuarios_linkedin.txt

# Password Spraying (1 intento por usuario)
kerbrute password-spray -d <DOMINIO_LOCAL> --dc <IP_DC> usuarios_validos.txt '<PASSWORD_PROBABLE>'
```

