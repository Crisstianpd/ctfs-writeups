# Informes de Seguridad Ofensiva (CTFs)
Este repositorio contiene informes estructurados de máquinas y laboratorios CTF de plataformas como Hack The Box, DockerLabs, PicoCTF y otras.

El objetivo no es solo documentar soluciones, sino también comprender las causas raíz y categorizar las vulnerabilidades presentes en cada máquina según el escenario de ataque, y extraer conocimiento reutilizable y aplicable para los posibles escenarios reales.

## 📚 Estructura de Navegación
Los informes se organizan lógicamente según la siguiente jerarquía:

Escenario -> Tipo de Vulnerabilidad -> Máquinas
> Cada máquina puede aparecer en múltiples categorías de vulnerabilidad, según las técnicas utilizadas.

---

## 🔎 Salto rápido
- [Ataques a aplicaciones web](#ataques-a-aplicaciones-web)
- [Escalada de privilegios](#escalada-de-privilegios)
- [Active Directory](#active-directory)
- [Criptografía](#criptografía)

---

## Ataques a aplicaciones web

### Inyección
- SQL Injection
  - [Puzzle (DockerLabs)](https://youtu.be/pRS9fz8IHfc?si=HZUYkRMfNeI1L6PG)
- Command Injection
  
### Autenticación y control de acceso
- IDOR
- Broken Access Control
  
### Ataques del lado del cliente
- Stored XSS
- Reflected XSS

## Escalada de privilegios

### Linux
- SUID Abuse
- Cron Job Abuse

### Windows
- Unquoted Service Path
- Token Impersonation

## Active Directory
- Kerberoasting
- AS-REP Roasting

## Criptografía
- Weak RSA
- ECB Mode Leakage

---

## Estructura del Informe
Cada informe incluye:
- Metodología de reconocimiento
- Estrategia de enumeración
- Ruta de explotación
- Escalada de privilegios (si corresponde)
- Análisis de la causa raíz
> Al inicio de cada informe de depuración, se incluyen metadatos para la indexación futura y la posible automatización.

## Propósito de este repositorio
Este repositorio sirve como:
- Una base de conocimientos estructurada sobre seguridad ofensiva
- Un archivo categorizado de escenarios de ataques reales
- Un sistema de referencia para patrones de vulnerabilidad
- Una base para la automatización y la indexación de búsquedas futuras

## 📊 Estadísticas
- Web: 0 máquinas
