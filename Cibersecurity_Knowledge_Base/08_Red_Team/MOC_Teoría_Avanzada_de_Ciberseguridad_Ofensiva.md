---

tags: [moc, red_team, active_directory, evasion, explotacion, redes, criptografia, ofensiva]
---
# 🎯 MOC - Red Team & Offensive Security

## 1. Reconocimiento, OSINT y Perfilado (Reconnaissance & Surface Mapping)
- [[Reconocimiento_Externo_y_OSINT]]
  > *Descubrimiento de activos expuestos, enumeración de subdominios, fuga de credenciales en fuentes abiertas y perfilado de infraestructura externa.*
- [[Perfilado_de_Identidad_y_Superficie_de_Ataque]]
  > *Mapeo de empleados, tecnologías expuestas, pasarelas de correo y vectorización de puntos de entrada en entornos multi-cloud.*

## 2. Acceso Inicial e Ingeniería Social (Initial Access & Phishing)
- [[Phishing_AiTM_Evilginx3_y_Bypass_MFA]]
  > *Intercepción de credenciales y cookies de sesión mediante proxies reversos para evadir MFA en servicios Cloud y VPNs.*
- [[Payload_Delivery_HTML_Smuggling_y_LNK_Abuse]]
  > *Entregas de código malicioso en memoria del navegador utilizando HTML5/JS para eludir inspección de tráfico y pasarelas de correo.*
- [[Explotacion_de_Servicios_Explicitos_y_Vulnerabilidades_Perimetrales]]
  > *Identificación y explotación de fallos en VPNs, firewalls y aplicaciones web expuestas para obtención de acceso inicial.*

## 3. Abuso de Identidad Híbrida & Multi-Cloud (Active Directory, Entra ID & AWS/GCP)
- [[Abuso_de_AD_CS_Active_Directory_Certificate_Services]]
  > *Vectores de escalada de privilegios y persistencia mediante plantillas de certificados mal configuradas (ESC1 a ESC17).*
- [[Ataques_de_Kerberos_Kerberoasting_ASREPRoasting_y_Delegation]]
  > *Explotación de fallos de autenticación en Kerberos para extracción de hashes TGS/AS-REP y abuso de delegación (Unconstrained/Constrained/RBCD).*
- [[Entra_ID_Primary_Refresh_Token_PRT_y_Abuse]]
  > *Extracción y uso fraudulento del PRT en sistemas Windows unidos a Entra ID para movimiento lateral directo a la nube.*
- [[Seguridad_Cloud_AWS_GCP_y_Abuso_de_IMDS]]
  > *Explotación de IMDSv1/v2, compromiso de roles IAM, movimiento lateral en cloud y persistencia en buckets o K8s.*

## 4. Movimiento Lateral, Pivoting y Redes Segmentadas (Lateral Movement & Networking)
- [[Pivoting_Avanzado_Ligolo_ng_Chisel_y_SOCKS]]
  > *Técnicas de tunelización e interconexión de redes segmentadas mediante proxies SOCKS, interfaces TUN/TAP y herramientas modernas.*
- [[NTLM_Relaying_Coercion_y_ADIDNS_Abuse]]
  > *Coacción de autenticación NTLM (PetitPotam, PrinterBug) y reenrutamiento hacia endpoints de autenticación o AD CS.*
- [[Abuso_de_ACLs_y_Relaciones_de_Confianza_en_AD]]
  > *Modificación de permisos en objetos de Active Directory (GenericAll, WriteDacl) y saltos de dominio mediante relaciones de confianza.*

## 5. Evasión de Defensas & Exploit Development (Defense Evasion)
- [[Evasion_de_API_Hooking_Direct_y_Indirect_Syscalls]]
  > *Invocación directa o indirecta de System Calls en ensamblador para evitar la inspección de hooks introducidos por EDRs en ntdll.dll.*
- [[Bypass_de_AMSI_y_ETW_en_Memoria]]
  > *Desactivación en memoria de AMSI (Antimalware Scan Interface) y ETW (Event Tracing for Windows) para cegar la telemetría del agente de seguridad.*
- [[Sleep_Obfuscation_y_Evasion_de_EDR_in_Memory]]
  > *Ofuscación y cifrado de memoria del proceso implantado durante los periodos de reposo para eludir escaneos volumétricos de memoria.*

## 6. Escalada de Privilegios Local & Post-Explotación (Windows, Linux & macOS)
- [[Process_Injection_Técnicas_Avanzadas_y_HW_Breakpoints]]
  > *Inyección de Shellcode mediante manipulaciones de contexto (Thread Context), Hardware Breakpoints y técnicas de carga limpia.*
- [[Explotacion_de_Kernel_y_BYOVD_Bring_Your_Own_Vulnerable_Driver]]
  > *Uso de controladores legítimos vulnerables firmados para cegar o desarmar agentes EDR desde Ring 0.*
- [[Post_Explotacion_Linux_y_macOS_TCC_y_eBPF]]
  > *Escalada de privilegios y persistencia en Linux/macOS mediante TCC Bypasses, módulos PAM y payloads en memoria.*

## 7. Infraestructura C2 & Mando y Control (Command & Control)
- [[Arquitectura_C2_Command_and_Control_y_Redirectors]]
  > *Diseño de infraestructura de mando y control oculta utilizando Proxies Reversos, Malleable PE, CDN Hiding y Cloudflare Workers.*
- [[C2_Frameworks_Sliver_Havoc_y_Cobalt_Strike]]
  > *Operación y configuración avanzada de frameworks C2 modernos de nivel profesional para entornos empresariales.*

## 8. Exfiltración de Datos & Impacto de Negocio (Exfiltration & Actions on Objectives)
- [[Exfiltracion_de_Datos_Canales_Ocultos_y_Bypass_de_DLP]]
  > *Técnicas de exfiltración encubierta de información sensible mediante tunelización DNS, almacenamiento cloud legítimo (APIs) y cifrado.*
- [[Demostracion_de_Impacto_y_Simulacion_de_Ransomware]]
  > *Simulación segura de cifrado/destrucción de activos y extracción de pruebas de impacto para auditorías directivas.*

---

## 🌐 Referencias Oficiales y Documentación
- [MITRE ATT&CK® Framework](https://attack.mitre.org/) - *Matriz global de tácticas, técnicas y procedimientos (TTPs) de adversarios.*
- [Microsoft Learn - Security & Identity](https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/ad-ds-getting-started) - *Documentación oficial sobre arquitectura de Active Directory y servicios de identidad.*
- [CISA Cybersecurity Advisories](https://www.cisa.gov/news-events/cybersecurity-advisories) - *Alertas y avisos de seguridad gubernamentales sobre amenazas activas.*
- [Certipy Repository (SpecterOps Research)](https://github.com/ly4k/Certipy) - *Herramienta y documentación oficial para la enumeración y explotación de AD CS (ESC1-ESC17).*
- [SysWhispers3 Repository](https://github.com/klezVirus/SysWhispers3) - *Implementación de referencia para el uso de Indirect Syscalls y bypass de hooks.*