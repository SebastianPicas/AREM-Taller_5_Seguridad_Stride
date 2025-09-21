# 🛠️ Taller 5: Evaluación de Seguridad con STRIDE

Este repositorio muestra el trabajo por los estudiantes Julián Alvarado, Julián Durán, Sebastian Piñeros Castellanos para el taller de BPMN el cual tiene como objetivo modelar un proceso de negocio real del cliente utilizando la notación BPMN, identificando eventos, actividades, decisiones, actores involucrados y puntos críticos del flujo.

## 1. Resumen ejecutivo

Este informe documenta el análisis de amenazas (metodología STRIDE) aplicado al proceso *"Envío de extracto del cliente"* y propone una solución técnica: la creación de un ejecutable (Python → binario) que automatice el análisis de extractos financieros en PDF y migre la información a un formato tipo Excel o a una versión simplificada del extracto. Se detallan los riesgos identificados, contramedidas recomendadas y un diseño de alto nivel del software con pasos de implementación y despliegue.

---

## 2. Proceso evaluado

Breve descripción del flujo:
1. Identificar cliente.
2. Enviar correo al jefe encargado (Zipa / Chía).
3. Revisión del extracto por parte del área.
4. Aprobación del proceso.
5. Confirmación al cliente.

---

## 3. STRIDE (resumen)

### Spoofing
- Autenticación fuerte (MFA) para usuarios y roles críticos (revisores, aprobadores).
- Uso de certificados o firma digital para correos sensibles.
- Validación automática de remitente (SPF/DKIM/DMARC) en servidor de correo.

### Tampering
- Integridad: almacenar hashes (SHA-256) de PDFs y registros de cambios.
- Versionado y control de accesos (RBAC).
- Firmas digitales en documentos aprobados.

### Repudiation
- Auditar y registrar eventos con timestamps y usuarios (logs inmutables).
- Guardar copias cifradas de los documentos originales y evidencias de acciones.

### Information Disclosure
- Cifrado en reposo y en tránsito (TLS, cifrado base de datos).
- Minimización de datos: solo exponer campos necesarios.
- Controles de acceso por rol y enrutamiento de correos cifrados cuando sea necesario.

### Denial of Service
- Rate limiting en servicios que reciben PDFs y en endpoints de la herramienta.
- Monitorización y alertas de anomalías.
- Infraestructura redundante para los servicios críticos.

### Elevation of Privilege
- Principio de menor privilegio (menos permisos por defecto).
- Revisión periódica de roles y permisos.
- Validaciones de autorización en cada acción sensible.

---

## 4. Solución propuesta (visión general)

Crear un **ejecutable Python** que:
- Reciba uno o varios PDFs de extractos financieros.
- Extraiga la información relevante (movimientos, saldos, fechas, titular).
- Genere:
  - Un archivo Excel (.xlsx) con estructura tabular, o
  - Una versión simplificada (CSV / JSON) con campos clave.
- Genere logs, reportes de errores y un resumen de calidad (qué páginas no pudieron parsearse, confianza OCR).
- Incluya validaciones básicas, firma/huella del documento original y controles de acceso (si se integra con servidor).

Motivaciones:
- Eliminar tareas manuales y errores humanos.
- Estandarizar la migración a un formato usable por análisis y sistemas internos.
- Mejorar trazabilidad y soporte a auditoría.

---

## 5. Requerimientos funcionales y no funcionales

### Funcionales
- Soportar entrada por carpeta, archivo o arrastrar y soltar.
- Detectar y procesar PDFs nativos y PDFs escaneados (requiere OCR).
- Mapear campos del extracto a columnas predefinidas.
- Exportar a Excel (.xlsx), CSV y JSON.
- Reporte con métricas de extracción y calidad por archivo.

### No funcionales
- Seguridad: cifrado de datos sensibles en disco si se aloja localmente.
- Rendimiento: procesar en paralelo por CPU-bound IO.
- Portabilidad: ejecutar como binario en Windows/Linux (PyInstaller).
- Trazabilidad: logs con nivel DEBUG/INFO/ERROR y auditoría.
