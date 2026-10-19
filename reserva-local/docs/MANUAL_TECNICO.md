# Manual Técnico de Arquitectura y Desarrollo - Reserva Local 🛠️
**Documentación de Ingeniería de Software, Seguridad y Base de Datos**  
*Plataforma: PHP 8.x Nativo | MySQL 5.7+ / 8.x (InnoDB) | HTML5 / CSS3 / Vanilla JS*

---

## 1. Visión General del Sistema y Restricciones Tecnológicas

**Reserva Local** es una aplicación web transaccional construida bajo el principio de **cero dependencias externas**:
- **Prohibido el uso de:** Composer, Node.js, npm, Laravel, Symfony, React, Vue, jQuery, Bootstrap o Tailwind.
- **Backend:** PHP 8.2 nativo con arquitectura limpia basada en Front Controller, Modelos Activos (Active Record simplificado), Capa de Servicios y Controladores delegados.
- **Base de Datos:** MySQL con motor de almacenamiento **InnoDB** obligatorio (requerido para soporte de transacciones ACID y bloqueos pesimistas de fila `SELECT ... FOR UPDATE`).
- **Frontend:** HTML5 semántico, CSS3 puro con variables personalizadas (`var(--primary)`) y JavaScript modular vanilla (`fetch` nativo y Geolocation API).

---

## 2. Estructura de Archivos del Proyecto

```text
reserva-local/
├── app/                              # Lógica central del sistema (fuera del DocumentRoot público)
│   ├── bootstrap.php                 # Autoload, inicialización de sesión segura y helpers
│   ├── config/
│   │   ├── config.php                # Variables globales, entorno, timeouts y base de datos
│   │   └── database.php              # Patrón Singleton para conexión PDO
│   ├── helpers/
│   │   └── helpers.php               # Funciones de escape XSS, CSRF, auth, formato y URLs
│   ├── middleware/                   # Control de acceso y filtros
│   ├── models/                       # Capa de datos y persistencia SQL
│   │   ├── Usuario.php               # Autenticación, roles, rate limit y recuperación
│   │   ├── Negocio.php               # Consultas con fórmula Haversine y catálogo
│   │   ├── Servicio.php              # Precios, duración y disponibilidad
│   │   ├── Profesional.php           # Especialistas y vinculación de cuentas
│   │   ├── Horario.php               # Horarios comerciales, descansos y excepciones
│   │   ├── Reserva.php               # Estados y códigos únicos
│   │   ├── Notificacion.php          # Cola de recordatorios
│   │   └── Auditoria.php             # Trazabilidad de seguridad
│   ├── services/                     # Lógica de negocio pura
│   │   ├── DisponibilidadService.php # Algoritmo de cálculo de bloques libres
│   │   ├── ReservaService.php        # Transacciones ACID y SELECT FOR UPDATE
│   │   ├── NotificacionService.php   # Adaptadores desacoplados de mensajería
│   │   └── ReporteService.php        # Consultas de analítica y facturación
│   └── views/                        # Vistas y componentes HTML
│       ├── layouts/                  # header.php, footer.php, sidebar.php, 403.php, 404.php
│       └── partials/
├── database/
│   ├── schema.sql                    # Definición DDL de 16 tablas con claves foráneas e índices
│   └── datos_demo.sql                # Población de prueba para El Salvador
├── docs/                             # Manuales del sistema
│   ├── MANUAL_USUARIO.md             # Guía operativa para cada rol
│   └── MANUAL_TECNICO.md             # Especificación técnica de ingeniería
├── public/                           # Directorio raíz accesible vía HTTP
│   ├── index.php                     # Portada y enrutador principal
│   ├── login.php                     # Autenticación con prevención de fuerza bruta
│   ├── registro.php                  # Registro de clientes y propietarios
│   ├── explorar.php                  # Búsqueda geoespacial y filtros
│   ├── negocio.php                   # Ficha de establecimiento
│   ├── reservar.php                  # Wizard de agendamiento
│   ├── mis-citas.php                 # Consulta y cancelación de citas
│   ├── perfil.php                    # Datos personales y cambio de clave
│   ├── logout.php                    # Cierre seguro de sesión
│   ├── api/                          # Endpoints JSON (disponibilidad, reservar, cancelar)
│   ├── propietario/                  # Panel de administración de negocio
│   ├── profesional/                  # Panel de agenda para especialistas
│   ├── administrador/                # Panel de superadministrador global
│   └── assets/                       # CSS puro, JS nativo y gráficos vectoriales SVG
├── scripts/
│   └── recordatorios.php             # Worker batch para Cron / Task Scheduler
├── storage/
│   ├── logs/                         # Registro de errores y notificaciones
│   └── uploads/                      # Almacenamiento seguro de imágenes
├── install.php                       # Instalador automático CLI / Web
└── README.md                         # Instrucciones de despliegue y puesta en marcha
```

---

## 3. Esquema de Base de Datos y Modelo Entidad-Relación

El sistema cuenta con **16 tablas normalizadas** bajo el motor **InnoDB** con codificación `utf8mb4_unicode_ci`:

1. `roles`: Catálogo de roles (`cliente`, `propietario`, `profesional`, `administrador`).
2. `usuarios`: Cuentas de usuario con hash de contraseña bcrypt, teléfono y estado (`activo`, `inactivo`, `bloqueado`).
3. `usuario_roles`: Relación N:M que permite a un usuario poseer múltiples roles simultáneamente.
4. `password_resets`: Tokens SHA-256 aleatorios de un solo uso con fecha de expiración (60 minutos).
5. `login_attempts`: Registro de intentos fallidos por IP y correo con bloqueo temporal tras 5 intentos.
6. `negocios`: Establecimientos con coordenadas decimales (`DECIMAL(10,7)`), categoría, dirección, zona horaria y estado.
7. `negocio_usuarios`: Vínculo N:M que define la propiedad y administración de uno o más negocios por parte de un usuario.
8. `servicios`: Servicios ofrecidos por negocio con precio en USD (`DECIMAL(10,2)`) y duración en minutos (`INT`).
9. `profesionales`: Especialistas vinculados al negocio y opcionalmente a una cuenta de usuario para gestión de su agenda.
10. `profesional_servicios`: Relación N:M que define qué servicios específicos puede realizar cada especialista.
11. `horarios_negocio`: Horarios de apertura y cierre por día de la semana (1 = Lunes .. 7 = Domingo) y bandera `es_laborable`.
12. `horarios_profesional`: Horario laboral del especialista con franjas de descanso / almuerzo (`descanso_inicio`, `descanso_fin`).
13. `excepciones_horario`: Cierres totales o parciales por fecha (días festivos, remodelación, permisos).
14. `reservas`: Citas agendadas con código alfanumérico único, precio y duración congelados, horas exactas de inicio y fin, y estado (`pendiente`, `confirmada`, `cancelada`, `completada`, `no_asistio`).
15. `notificaciones`: Cola de mensajes con estado (`pendiente`, `enviada`, `fallida`), reintentos e información de programación.
16. `auditoria`: Bitácora inmutable de acciones administrativas con usuario, acción, entidad, dirección IP y User-Agent.

---

## 4. Algoritmos Críticos de Negocio

### 4.1 Búsqueda Geoespacial mediante Fórmula Haversine en MySQL
Para calcular la distancia ortodrómica sobre la superficie esférica de la Tierra (radio medio = 6,371 km) directamente en MySQL, se implementa la fórmula de Haversine dentro de la consulta preparada:

```sql
SELECT n.*, 
    (6371 * ACOS(
        LEAST(1.0, GREATEST(-1.0,
            COS(RADIANS(:lat1)) * COS(RADIANS(n.latitud)) * COS(RADIANS(n.longitud) - RADIANS(:lng))
            + SIN(RADIANS(:lat2)) * SIN(RADIANS(n.latitud))
        ))
    )) AS distancia_km
FROM negocios n
WHERE n.estado = 'activo'
HAVING distancia_km <= :radio
ORDER BY distancia_km ASC;
```

**Decisión técnica importante:**  
Se emplea `LEAST(1.0, GREATEST(-1.0, ...))` para prevenir fallos por imprecisión de punto flotante donde valores como `1.0000000000000002` provocarían que `ACOS()` devolviera `NULL` o produjera un error matemático.

### 4.2 Motor de Disponibilidad Dinámica (`DisponibilidadService.php`)
Calcula los bloques horarios disponibles para una fecha determinada aplicando el siguiente árbol de decisión:
1. **Validación de Fecha:** Se descartan fechas pasadas. Si la fecha es el día de hoy, se descartan los slots anteriores a la hora actual (+15 minutos de holgura).
2. **Validación de Negocio:** Verifica si el negocio abre el día de la semana (`horarios_negocio.es_laborable = 1`).
3. **Excepciones del Negocio:** Comprueba si existe un `cierre_total` en `excepciones_horario` para esa fecha.
4. **Filtrado de Especialistas:** Identifica qué profesionales activos ofrecen el servicio seleccionado.
5. **Cálculo de Jornada y Descanso:**
   - La jornada efectiva es: $\text{Inicio} = \max(\text{AperturaNegocio}, \text{InicioProfesional})$, $\text{Fin} = \min(\text{CierreNegocio}, \text{FinProfesional})$.
   - Se omite cualquier slot que se solape con el intervalo de descanso/almuerzo del profesional:
     $$\text{slot\_inicio} < \text{descanso\_fin} \quad \text{AND} \quad \text{slot\_fin} > \text{descanso\_inicio}$$
6. **Colisiones con Citas Existentes:** Se evalúa que no solape con reservas activas (`estado != 'cancelada'`).
7. **Generación de Slots:** Se construyen intervalos con incremento de 30 minutos agrupando los especialistas disponibles en cada franja.

### 4.3 Control de Concurrencia y Prevención de Solapamientos (`ReservaService.php`)
Para garantizar consistencia absoluta ante solicitudes simultáneas de dos clientes reservando el mismo turno al mismo milisegundo:

```php
$pdo->beginTransaction();

// 1. Bloqueo pesimista de fila sobre el especialista para serializar operaciones
$stmtLock = $pdo->prepare("SELECT id, estado FROM profesionales WHERE id = ? FOR UPDATE");
$stmtLock->execute([$profesionalId]);

// 2. Comprobar solapamiento de horario
// Dos intervalos [I1, F1] e [I2, F2] colisionan si: I1 < F2 AND F1 > I2
$stmtOverlap = $pdo->prepare("
    SELECT id FROM reservas 
    WHERE profesional_id = ? 
      AND fecha = ? 
      AND estado != 'cancelada'
      AND hora_inicio < ? 
      AND hora_fin > ?
");
$stmtOverlap->execute([$profesionalId, $fecha, $horaFinSolicitada, $horaInicioSolicitada]);

if ($stmtOverlap->fetch()) {
    $pdo->rollBack();
    throw new Exception("El turno seleccionado ya no está disponible.");
}

// 3. Inserción de la cita con precio y duración congelados
// ...
$pdo->commit();
```

---

## 5. Arquitectura de Seguridad Implementada

1. **Gestión Criptográfica de Contraseñas:**  
   Se utiliza la API nativa de PHP `password_hash()` con algoritmo `PASSWORD_BCRYPT` y coste por defecto (10). Ninguna contraseña se almacena en texto plano.
2. **Mitigación de Cross-Site Request Forgery (CSRF):**  
   Todas las operaciones de mutación (formularios `POST` y endpoints API) validan un token pseudoaleatorio criptográficamente seguro de 32 bytes (`random_bytes(32)`) contrastado mediante `hash_equals()`.
3. **Inmunidad contra SQL Injection:**  
   El 100% de las consultas a MySQL se ejecutan a través de **PDO con consultas preparadas** y parámetros tipados. `PDO::ATTR_EMULATE_PREPARES` se establece en `false` para delegar la preparación real al motor de base de datos.
4. **Prevención de Cross-Site Scripting (XSS):**  
   Toda salida impresa en plantillas pasa por la función auxiliar `e($valor)` que implementa `htmlspecialchars($valor, ENT_QUOTES, 'UTF-8')`.
5. **Protección contra Fuerza Bruta en Autenticación:**  
   La tabla `login_attempts` registra intentos fallidos por dirección IP y correo electrónico. Al alcanzar 5 intentos consecutivos, la cuenta se bloquea por 15 minutos. Un login exitoso restablece el contador a cero.
6. **Endurecimiento de Sesiones:**  
   Directivas de cookie configuradas:
   - `httponly = true` (bloquea el acceso a la cookie desde JavaScript).
   - `samesite = Lax` (protección contra ataques CSRF de origen cruzado).
   - `secure = true` (activo automáticamente si la conexión se realiza bajo HTTPS).
   - `session_regenerate_id(true)` (ejecutado tras login exitoso para prevenir Session Fixation).
7. **Control de Acceso Basado en Roles (RBAC):**  
   Los controladores y vistas críticas invocan `require_role('propietario')`, `require_role('profesional')` o `require_role('administrador')`. Ninguna operación confía en identificadores o parámetros enviados por el navegador.

---

## 6. Especificación de Endpoints API Internos

### 6.1 `GET /api/disponibilidad.php`
- **Parámetros:** `negocio_id` (int), `servicio_id` (int), `fecha` (YYYY-MM-DD), `profesional_id` (opcional).
- **Respuesta 200 OK:**
  ```json
  {
    "negocio_id": 1,
    "servicio": { "id": 1, "nombre": "Corte Clásico", "precio": 8.0, "duracion_minutos": 45 },
    "fecha": "2026-10-25",
    "slots": [
      {
        "hora_inicio": "09:00:00",
        "hora_inicio_formato": "09:00",
        "hora_fin": "09:45:00",
        "hora_fin_formato": "09:45",
        "profesionales": [{ "id": 1, "nombre": "Carlos Méndez" }]
      }
    ]
  }
  ```

### 6.2 `POST /api/reservar.php`
- **Cabeceras:** `X-CSRF-Token` o campo `csrf_token` en `FormData`.
- **Payload:** `negocio_id`, `servicio_id`, `profesional_id`, `fecha`, `hora_inicio`, `notas_cliente`.
- **Respuesta 201 Created:**
  ```json
  {
    "success": true,
    "codigo_reserva": "RES-20261019-XXXXXX",
    "redirect": "/reserva-local/public/mis-citas.php?reserva=RES-20261019-XXXXXX"
  }
  ```

### 6.3 `POST /api/cancelar.php`
- **Payload:** `reserva_id`, `motivo`, `csrf_token`.
- **Respuesta 200 OK:** `{ "success": true, "mensaje": "Cita cancelada" }`.

### 6.4 `POST /api/cambiar_estado.php`
- **Payload:** `reserva_id`, `estado` (`confirmada`|`completada`|`no_asistio`|`cancelada`), `csrf_token`.
- **Respuesta 200 OK:** `{ "success": true, "nuevo_estado": "completada" }`.

---

## 7. Sub-sistema de Recordatorios y Notificaciones Batch

El procesamiento en segundo plano está desacoplado mediante una interfaz de adaptadores:
- `NotificationSenderInterface`: Contrato para despacho de mensajes.
- `LogNotificationSender`: Adaptador predeterminado que registra en `storage/logs/recordatorios.log`.
- `MailNotificationSender`: Adaptador para envío SMTP / correo nativo vía variables de entorno.

### Ejecución periódica (Cron / Programador de Tareas):
```bash
php scripts/recordatorios.php
```
El worker:
1. Extrae notificaciones con `estado = 'pendiente'` y `programada_para <= NOW()`.
2. Aplica control de deduplicación y conteo de reintentos (`intentos < 3`).
3. Marca como `enviada` con marca de tiempo o incrementa fallos registrando el mensaje de error.

---

## 8. Consideraciones de Despliegue en Producción

1. **Requisito HTTPS para Geolocation API:**  
   Los navegadores modernos restringen la API `navigator.geolocation` a orígenes seguros (`localhost` o dominios bajo HTTPS con certificado SSL). Para producción en internet es indispensable activar HTTPS.
2. **Configuración de Variables de Entorno en Servidor:**
   ```ini
   APP_ENV=production
   APP_DEBUG=false
   APP_TIMEZONE=America/El_Salvador
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_NAME=reserva_local
   DB_USER=usuario_prod
   DB_PASS=PasswordFuerte123!
   MAIL_DRIVER=email
   SMTP_HOST=smtp.mailgun.org
   SMTP_PORT=587
   SMTP_USER=postmaster@tudominio.com
   SMTP_PASS=clave_smtp
   ```
