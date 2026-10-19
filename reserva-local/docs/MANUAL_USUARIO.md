# Manual de Usuario - Reserva Local ✂️
**Sistema de Descubrimiento y Reserva de Citas en Barberías y Salones de Belleza**  
*Configuración Regional: El Salvador (Punto Cero: San Salvador / AMSS)*

---

## 1. Introducción al Sistema
**Reserva Local** es una plataforma web desarrollada para descubrir barberías, peluquerías y salones de belleza cercanos, consultar disponibilidad de turnos en tiempo real y agendar citas de forma instantánea sin intermediarios, llamadas telefónicas ni solapamientos de horario.

El sistema cuenta con 4 roles de usuario:
1. **Cliente:** Descubre negocios por GPS o zona, selecciona servicios y agenda sus citas.
2. **Propietario:** Administra el establecimiento, su catálogo de servicios, su equipo de especialistas, horarios semanales, cierres y reportes financieros.
3. **Profesional:** Consulta su agenda diaria de atención personalizada y gestiona el estado de sus citas.
4. **Administrador Global:** Supervisa la plataforma, activa o suspende cuentas y negocios, y audita los eventos del sistema.

---

## 2. Acceso al Sistema y Cuentas de Demostración

### Dirección web:
- **Servidor local:** [http://localhost:8000](http://localhost:8000) o [http://localhost/reserva-local/public/](http://localhost/reserva-local/public/)

### Cuentas preconfiguradas para pruebas rápidas:
> **Contraseña universal para todas las cuentas demo:** `Demo123!`

| Rol | Correo Electrónico | Descripción |
| :--- | :--- | :--- |
| **Administrador Global** | `admin@reservalocal.test` | Control total del sistema y auditoría. |
| **Propietario 1** | `propietario1@reservalocal.test` | Dueño de *Barbería Clásica El Caballero* (Col. Escalón) y *Vintage Blade Studio* (Santa Tecla). |
| **Propietario 2** | `propietario2@reservalocal.test` | Dueña de *Salón & Spa Bella Donna* (San Benito). |
| **Profesional / Barbero** | `carlos@reservalocal.test` | Barbero especialista en El Caballero. |
| **Profesional / Estilista**| `sofia@reservalocal.test` | Estilista senior en Bella Donna. |
| **Cliente 1** | `cliente1@reservalocal.test` | Alejandro Morales (cuenta con historial de citas). |
| **Cliente 2** | `cliente2@reservalocal.test` | Camila Torres (cuenta con historial de citas). |

---

## 3. Guía del Cliente

### 3.1 Registro e Inicio de Sesión
1. Dirígete a la opción **"Registrarse"** en la barra superior.
2. Selecciona **"Soy Cliente"**, completa tu nombre, correo electrónico, teléfono y contraseña (mínimo 8 caracteres).
3. Una vez registrado, tu sesión se iniciará de forma automática.
4. Si ya posees cuenta, ingresa mediante **"Iniciar Sesión"** con tus credenciales.

### 3.2 Búsqueda de Negocios por Ubicación y GPS
En la sección **"Explorar Negocios"** dispones de dos modalidades de localización:
- **Ubicación GPS Automática:**
  - Presiona el botón **`📍 Usar mi ubicación GPS`**.
  - Tu navegador te solicitará permiso para acceder a tu ubicación. Al aceptarlo, el sistema calculará con precisión matemática la distancia en kilómetros hacia cada establecimiento desde tu posición actual en El Salvador.
  - Puedes ajustar el **Radio Máximo** (5 km, 10 km, 25 km, 50 km, etc.) para acotar la búsqueda.
  - *Botón demo:* Si deseas simular estar en el corazón de San Salvador (Colonia Escalón), presiona **`🎯 Probar GPS en San Salvador (Demo)`**.
- **Búsqueda Manual Alternativa:**
  - Si deniegas el permiso de GPS o estás en un equipo sin geolocalización, escribe tu colonia o municipio en el campo *"Ubicación Manual"* (ej. `Escalón`, `San Benito`, `Santa Tecla`, `Antiguo Cuscatlán`).
- **Filtros por Servicio y Categoría:**
  - Filtra entre **Barberías** o **Salones de Belleza**, o ingresa palabras clave (ej. `Corte`, `Barba`, `Manicura`, `Keratina`).

### 3.3 Visualización de la Ficha del Negocio
Al hacer clic en un establecimiento, accederás a su perfil completo:
- Dirección exacta, teléfono de contacto y mapa descriptivo.
- Catálogo de servicios con precios (en USD) y duración estimada.
- Lista del equipo de especialistas y sus especialidades.
- Tabla con los horarios de apertura y cierre de lunes a domingo.

### 3.4 Proceso de Reserva Paso a Paso (Wizard)
1. Presiona **"Reservar Cita"**.
2. **Paso 1 (Servicio):** Selecciona el corte o tratamiento que deseas.
3. **Paso 2 (Especialista y Fecha):** Elige tu barbero/estilista preferido (o la opción *"Cualquiera disponible"*) y selecciona el día en el calendario.
4. **Paso 3 (Horario en Tiempo Real):** El sistema consultará la disponibilidad en vivo y mostrará únicamente los bloques horarios libres (calculados según la duración del servicio, los descansos y las reservas existentes). Haz clic en el turno de tu preferencia.
5. **Paso 4 (Notas):** Agrega instrucciones opcionales para el especialista (ej. *"Corte con tijera suave"*).
6. **Confirmación:** Revisa el resumen lateral con el precio acordado y presiona **"Confirmar y Reservar Cita"**. Recibirás tu código único de reserva (ej. `RES-20261019-XXXXXX`).

### 3.5 Gestión de Citas ("Mis Citas")
- En el menú superior haz clic en **"📅 Mis Citas"**.
- Podrás filtrar entre: **Todas**, **Próximas**, **Historial / Completadas** y **Canceladas**.
- **Cancelar una cita:** Si surge un imprevisto, puedes cancelar turnos pendientes o confirmados presionando el botón **"Cancelar Cita"**. El horario quedará libre de inmediato para otros clientes.

### 3.6 Perfil y Contraseña
- En el menú **"Mi Perfil"** puedes actualizar tu nombre, teléfono y modificar tu contraseña de acceso ingresando tu clave actual.

---

## 4. Guía del Propietario de Negocio

El portal de propietarios está disponible en la barra superior (**"🏢 Mi Negocio"**):

### 4.1 Resumen General (Dashboard)
- Indicadores en tiempo real: citas agendadas para hoy, facturación proyectada, ingresos cobrados en el mes, especialistas activos y servicios en catálogo.
- Tabla de citas del día con selector de acción rápida para confirmar, completar o cancelar reservas al momento.

### 4.2 Calendario y Agendas por Especialista
- Vista completa de citas por fecha con filtros por barbero/estilista y por estado.
- Permite a los recepcionistas o dueños actualizar el estado de los turnos en cualquier momento.

### 4.3 Gestión del Catálogo de Servicios
- **Crear Servicio:** Ingresa nombre, descripción, precio en dólares y duración en minutos (ej. 30, 45, 60 min).
- **Editar / Desactivar:** Modifica tarifas o desactiva servicios temporalmente para que no aparezcan en el portal público.

### 4.4 Personal y Especialistas
- Da de alta a tus barberos o estilistas.
- **Asignar Servicios:** Marca qué servicios específicos puede realizar cada miembro del equipo.
- **Vincular Cuenta de Usuario:** Si el especialista tiene cuenta registrada con su correo, escríbelo aquí para que pueda iniciar sesión y ver su agenda personal.

### 4.5 Horarios de Atención y Descansos
- **Horarios del Negocio:** Define la hora de apertura y cierre de cada día de la semana (Lunes a Domingo), o márcalo como día cerrado.
- **Horarios por Especialista:** Configura la jornada laboral de cada barbero y sus franjas de descanso o almuerzo (ej. descanso de 14:00 a 15:00) para evitar que el sistema asigne citas en su hora de comida.

### 4.6 Cierres y Excepciones por Fecha
- Programa días festivos, vacaciones de especialistas o cierres por mantenimiento.
- El sistema bloqueará automáticamente la disponibilidad para esas fechas.

### 4.7 Reportes Financieros y de Demanda
- Consulta de ingresos totales en cualquier rango de fechas.
- Desglose por estado de cita (completadas, confirmadas, canceladas, no asistió).
- **Top 5 Servicios más solicitados:** cantidad de citas, ingresos generados y precio promedio.
- **Horas de Mayor Demanda (Horas Pico):** gráfico de barras para conocer en qué horas del día se concentran más clientes y optimizar turnos de personal.
- Rendimiento comparativo por especialista.

---

## 5. Guía del Especialista / Profesional

Al ingresar con una cuenta vinculada a un especialista (ej. `carlos@reservalocal.test`):
1. Accede a **"📋 Mi Agenda"** en la barra superior.
2. Consulta tus citas programadas para **Hoy**, **Mañana** o cualquier fecha futura.
3. Lee las notas o preferencias enviadas por el cliente.
4. Cambia el estado de la cita conforme la atiendas:
   - **Confirmada:** Cita lista para atenderse.
   - **Completada:** Servicio finalizado exitosamente.
   - **No Asistió:** El cliente no se presentó a su turno.
   - **Cancelar:** En caso de emergencia del especialista.

---

## 6. Guía del Administrador Global

Al ingresar con la cuenta de superadministrador (`admin@reservalocal.test`):
- **Panel Global:** Métricas de toda la plataforma (usuarios totales, negocios registrados, volumen global de citas y registro de auditoría).
- **Gestión de Usuarios:** Listado de todas las cuentas con posibilidad de cambiar el estado de acceso (`activo`, `inactivo`, `bloqueado`).
- **Gestión de Negocios:** Supervisión de establecimientos para activarlos, suspenderlos o marcarlos como pendientes.
- **Auditoría del Sistema:** Bitácora inmutable con fecha, hora, dirección IP, usuario y detalle de cada acción administrativa ejecutada en el sistema.

---

## 7. Recuperación de Contraseñas
1. En la pantalla de inicio de sesión haz clic en **"¿Olvidaste tu contraseña?"**.
2. Escribe tu correo electrónico registrado.
3. Se generará un enlace seguro de recuperación con un token de un solo uso que expira automáticamente en 60 minutos.
4. Ingresa tu nueva contraseña para restablecer el acceso a tu cuenta.
