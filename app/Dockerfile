##FROM python:3.10
##WORKDIR /app
##COPY . .
##RUN pip install -r requirements.txt
##CMD ["python", "app.py"]

# 1. Imagen base ligera
FROM python:3.10-slim

# Bonus
# Healthcheck Para que Docker pueda probar el funcionamiento de la app cada cierto tiempo.
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

# 2. Variables de entorno para Python
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# 3. Directorio de trabajo
WORKDIR /app

# 4. Seguridad: Crear un usuario no-root
RUN adduser --disabled-password --gecos "" appuser

# 5. Optimización de caché: Copiar solo dependencias primero
COPY requirements.txt .

# 6. Instalación limpia de librerías
RUN pip install --no-cache-dir -r requirements.txt

# 7. Copiar el resto del código y dar permisos
COPY . .
RUN chown -R appuser:appuser /app

# 8. Cambiar al usuario no-root
USER appuser

# 9. Comando de ejecución
CMD ["python", "app.py"]
