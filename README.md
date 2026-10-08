# Despliegue de Sistema de Control Horario - Limpiezas Lagares

Este repositorio contiene la pila Docker Compose para el sistema de control de presencia de **Limpiezas Lagares**, basado en **Kimai 2**.

## 🚀 Cómo se levanta

1. Copiar el archivo de variables de entorno y configurar los valores locales:
   ```bash
   cp .env.example .env
   ```
2. Iniciar los contenedores en segundo plano:
   ```bash
   docker compose up -d
   ```
3. Crear el usuario Administrador inicial en Kimai:
   ```bash
   docker exec -it lagares_app bin/console kimai:user:create admin admin@lagares.com ROLE_SUPER_ADMIN
   ```
4. Acceso a las aplicaciones:
   - **Kimai (App principal):** `http://localhost:8090`
   - **Adminer (Gestor BBDD):** `http://localhost:8081` (Servidor: `database`, Usuario: `kimai_user`, BD: `kimai`)

## 🔎 Comprobaciones realizadas

- Verificación del estado de los contenedores mediante `docker compose ps`.
- Acceso a la interfaz web móvil y registro exitoso de entradas/salidas de prueba para limpiadoras asociadas a clientes.
- Comprobación en Adminer de la creación automática de tablas en MariaDB (`kimai_timesheet`, `kimai_users`, etc.).

## ⚠️ Incidencias y Resoluciones

1. **Fallo de conexión inicial a la Base de Datos:**
   - _Problema:_ La aplicación Kimai intentaba arrancar antes de que MariaDB estuviera lista para recibir conexiones.
   - _Solución:_ Se añadió una sección `healthcheck` a MariaDB y `condition: service_healthy` en la dependencia de `app`.

2. **Puerto 8080 en conflicto:**
   - _Problema:_ El puerto `8080` de la máquina anfitriona ya estaba en uso.
   - _Solución:_ Se reasignó la publicación de puertos en `compose.yaml` mapeando el servicio a `8090:8001` y Adminer a `8081:8080`.
