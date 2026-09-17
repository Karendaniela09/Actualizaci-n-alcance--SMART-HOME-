# REPOSITORIO DE CAMBIOS Y ACTUALIZACIÓN DE ALCANCE — SMART HOME

**Proyecto:** Smart Home  
**Versión del documento:** 2.0 (Consolidado)  
**Fecha:** Septiembre de 2026  

---

## 1. Requerimientos Funcionales Eliminados (Simplificación de Flujo)

1. **RF Tarifas y Costos Monetarios:** Se elimina la configuración de tarifas eléctricas, historial de precios por kWh y cálculo de costos en moneda local. El sistema se enfocará exclusivamente en medir y monitorear el consumo en kWh y potencia (W).
2. **RF Zonas del Hogar:** Se elimina la jerarquía HOGAR -> ZONA -> DISPOSITIVO. Los dispositivos se asocian directamente al hogar (HOGAR -> DISPOSITIVO) para simplificar el flujo del usuario.
3. **RF Desactivar Hogar:** Se elimina la funcionalidad de desactivación lógica del hogar.
4. **RF Consultar / Configurar Recomendaciones:** Se descartan los módulos de sugerencias y reglas automáticas de recomendación.
5. **RF Modo Offline Completo:** Las operaciones de negocio requieren conexión obligatoria con el backend (NestJS). El almacenamiento local queda restringido a caché y preferencias.
6. **RF Configurar Notificaciones:** Se elimina el módulo de personalización compleja. El sistema enviará notificaciones por eventos predeterminados vía WebSocket/Push.
7. **RF Forzar Sincronización Manual:** La sincronización entre plataformas web y móvil será 100% automática y en tiempo real.
8. **RF Estrato Socioeconómico:** Se elimina el registro de estrato al no requerirse para liquidación tarifaria.

---

## 2. Matriz de Permisos y Reglas para Membresías del Hogar

### Roles en el Hogar
- **OWNER (Propietario):**
  - Puede listar miembros del hogar.
  - Puede invitar usuarios.
  - Puede ver invitaciones del hogar.
  - Puede cambiar roles (MEMBER <-> GUEST).
  - Puede remover miembros MEMBER o GUEST.
  - **Restricción:** El OWNER no puede eliminarse a sí mismo si es el único OWNER del hogar. Prohibido remover al último OWNER.
- **MEMBER (Miembro Autorizado):**
  - Puede ver miembros (si el backend lo permite).
  - Puede salir del hogar (status: LEFT).
  - **Restricción:** No puede cambiar roles ni remover otros miembros.
- **GUEST (Invitado):**
  - Puede ver lo permitido por Row Level Security (RLS).
  - Puede salir del hogar.
  - **Restricción:** No puede invitar ni modificar miembros.

### Ciclo de Vida de Invitaciones
- Una invitación aceptada no puede volverse a aceptar.
- Una invitación rechazada/revocada no puede ser aceptada posteriormente salvo que se genere una nueva.

---

## 3. Asistente de Vinculación de Dispositivos IoT (Shelly)

Se implementará un asistente de vinculación para Shelly 1PM Gen4 (y dispositivos compatibles) con dos mecanismos:
1. **Opción A (Detección Automática):** Escaneo de dispositivos en la red local.
2. **Opción B (Ingreso Manual de IP):** Fallback para ingresar la IP manualmente si falla la autodetección.

**Flujo Backend (NestJS + MQTT):**
- El backend identifica el dispositivo, configura el broker MQTT, registra el dispositivo directamente en el hogar y valida la conexión.
- El uso diario (control y lectura de consumo) se realiza exclusivamente desde Smart Home, no desde la app de Shelly.
