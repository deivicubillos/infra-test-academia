# Prueba Técnica - Infraestructura / DevOps

## Objetivo
Levantar el entorno y hacer que la aplicación sea accesible en:

http://localhost:8080

## Problema
Actualmente el sistema NO funciona correctamente.

## Tareas

1. Levantar el entorno
2. Identificar errores
3. Corregirlos
4. Documentar:
   - Qué fallaba
   - Cómo lo solucionaste
## Bonus

- Mejorar Dockerfile
- Agregar healthcheck
- Logs claros
- Preparar despliegue
- levantar servicio con traefik y modo swarm de docker
- integración continua

---
# Solución Prueba Técnica

Este repositorio contiene la resolución de la prueba técnica para el despliegue de una aplicación Flask optimizada, segura y escalable. Se migró de un entorno estático con errores de configuración a una **arquitectura elástica basada en Docker Swarm y Traefik**.

## 📊 Resumen de Cumplimiento (Basado en Reporte Técnico)

| Requerimiento Original        | Estado | Referencia en PDF                                                     |
| :---------------------------- | :----: | :-------------------------------------------------------------------- |
| **1. Levantar el entorno**    |   ✅    | Preparación de Debian 12 e instalación de Docker (Págs. 1-4).         |
| **2. Identificar errores**    |   ✅    | Diagnóstico de Networking, Puertos y Variables (Págs. 4-11).          |
| **3. Corregir errores**       |   ✅    | Resolución de Service Discovery y Mapeo de Puertos (Págs. 5, 10, 12). |
| **4. Documentar fallos**      |   ✅    | Reporte detallado de causa raíz y solución (Págs. 5, 9, 11).          |
| **Bonus: Mejorar Dockerfile** |   ✅    | Imagen Slim, Usuario No-root y Optimización de Capas (Págs. 14-22).   |
| **Bonus: Healthcheck**        |   ✅    | Implementación de pruebas de vida y estados de salud (Págs. 23-32).   |
| **Bonus: Logs Claros**        |   ✅    | Rotación de logs y persistencia en el Host (Págs. 33-37).             |
| **Bonus: Swarm + Traefik**    |   ✅    | Orquestación elástica y Service Discovery dinámico (Págs. 38-43).     |
| **Bonus: CI/CD**              |   ✅    | Pipeline GitHub Actions con escaneo de seguridad Trivy (Págs. 44-52). |

---

## Resumen Detallado por Punto

### 1. Fase de Diagnóstico y Reparación
**Problemas Detectados:**
*   **Error de Dependencia:** El servicio Nginx fallaba al buscar un host llamado `backend` (definido como `web` en el compose).
*   **Desajuste de Puertos:** Nginx intentaba conectar al puerto `8000`, mientras Flask escuchaba en el `5000`.
*   **Variable de Entorno:** La aplicación no reconocía el entorno de producción debido a que el archivo `.env` usaba `ENV` y el compose `ENVIRONMENT`.

**Solución:**
Se estandarizaron los nombres de servicio a `backend`, se corrigió el `proxy_pass` en `default.conf` al puerto `5000` y se unificaron las variables de entorno.
**Resultado:** Acceso exitoso a `http://localhost:8080` con el mensaje "Hello from production!".

### 2. Optimización Senior (Dockerfile)
**Acciones:**
*   **Reducción de Superficie de Ataque:** Cambio de imagen `python:3.10` (900MB) a `python:3.10-slim` (126MB).
*   **Seguridad:** Implementación de `appuser` (UID 1000) para evitar ejecución como root.
*   **Eficiencia:** Reordenamiento de capas (`COPY requirements.txt` antes del código) para aprovechar el caché de Docker.
**Resultado:** Construcción de imagen en <1s tras cambios de código y reducción del 85% en peso.

### 3. Resiliencia y Observabilidad
**Acciones:**
*   **Healthcheck:** Configuración de pruebas de vida cada 30s. Docker ahora marca el contenedor como `unhealthy` si la app se congela.
*   **Log Management:** Implementación de `json-file` con rotación (10MB, max 3 archivos) para proteger el almacenamiento del nodo Debian.
**Resultado:** Monitoreo preventivo y persistencia de logs en `/home/deivi/infra-test/logs/nginx`.

### 4. Orquestación Elástica (Swarm + Traefik)
**Acciones:**
*   **Migración:** Se activó el modo Swarm y se sustituyó Nginx por **Traefik v2**.
*   **Service Discovery:** Se eliminó la configuración manual de IPs; Traefik detecta las réplicas mediante `labels`.
*   **Escalabilidad:** Se configuraron 3 réplicas iniciales con capacidad de escalado en caliente.
**Resultado:** Balanceo de carga automático y **Self-healing** (Swarm recrea contenedores eliminados accidentalmente en segundos).

### 5. Integración Continua (GitHub Actions)
**Acciones:**
*   **Pipeline Automatizado:** Creación de `deploy.yml` que se dispara en cada `push`.
*   **Seguridad Activa:** Integración de **Trivy** para escanear vulnerabilidades en la imagen antes del despliegue.
*   **Pruebas de Integración:** El pipeline levanta un contenedor temporal y valida la respuesta con `curl --fail`.
**Resultado:** Flujo de entrega garantizado y validado por el robot de GitHub.

---
## Evidencias de Funcionamiento

Para ver el paso a paso detallado, comandos ejecutados y capturas de pantalla de todo el proceso de configuración en Debian 12, consulte el siguiente documento:

👉 **[Descargar Reporte Técnico Completo (PDF)](./docs/Reporte_Tecnico_Completo.pdf)**

**Candidato:** Deivi Cubillos  
**Stack:** Debian 12, Docker Swarm, Traefik, Flask, GitHub Actions.

---
