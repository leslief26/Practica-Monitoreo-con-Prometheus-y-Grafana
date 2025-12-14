# 📊 Sistema de Monitoreo con Prometheus y Grafana

Sistema completo de monitoreo usando Docker Compose en Windows.

## 🚀 Componentes

- **Prometheus**: Recolección y almacenamiento de métricas
- **Grafana**: Visualización de dashboards interactivos
- **Node Exporter**: Métricas del sistema (CPU, memoria, disco)
- **cAdvisor**: Métricas de contenedores Docker

## 📋 Requisitos

- Docker Desktop para Windows (instalado y corriendo)
- Docker Compose (incluido en Docker Desktop)
- Puertos disponibles: 3000, 8080, 9090, 9100

## 🔧 Instalación

### 1. Levantar servicios
```powershell
docker-compose up -d
```

### 2. Verificar estado
```powershell
docker-compose ps
```

Deberías ver 4 contenedores corriendo:
- prometheus
- grafana
- node-exporter
- cadvisor

## 🌐 Acceso a Servicios

| Servicio | URL | Credenciales |
|----------|-----|--------------|
| **Grafana** | http://localhost:3000 | admin / admin |
| **Prometheus** | http://localhost:9090 | N/A |
| **Node Exporter** | http://localhost:9100/metrics | N/A |
| **cAdvisor** | http://localhost:8080 | N/A |

## 📊 Configurar Grafana

### Paso 1: Acceder a Grafana

1. Abre tu navegador en http://localhost:3000
2. Usuario: `admin`
3. Contraseña: `admin`
4. (Opcional) Cambia la contraseña cuando te lo pida

### Paso 2: Agregar Prometheus como Data Source

1. Click en el ícono de **configuración** (⚙️) en el menú lateral
2. Selecciona **Data Sources**
3. Click en **Add data source**
4. Selecciona **Prometheus**
5. En la configuración:
   - **Name**: Prometheus
   - **URL**: `http://prometheus:9090`
6. Scroll hacia abajo y click en **Save & Test**
7. Deberías ver: ✅ "Data source is working"

### Paso 3: Importar Dashboards

#### Dashboard 1: Node Exporter Full (ID: 1860)

1. Click en el ícono **+** en el menú lateral → **Import**
2. En "Import via grafana.com" escribe: `1860`
3. Click en **Load**
4. Selecciona **Prometheus** como data source
5. Click en **Import**

#### Dashboard 2: Docker Container & Host Metrics (ID: 193)

1. Click en **+** → **Import**
2. Escribe: `193`
3. Click en **Load**
4. Selecciona **Prometheus**
5. Click en **Import**

## 📈 Métricas Disponibles

### Sistema (Node Exporter)
```promql
# Uso de CPU (%)
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memoria disponible (GB)
node_memory_MemAvailable_bytes / 1024 / 1024 / 1024

# Uso de disco (GB)
node_filesystem_avail_bytes{mountpoint="/"} / 1024 / 1024 / 1024

# Tráfico de red recibido
rate(node_network_receive_bytes_total[5m])
```

### Contenedores (cAdvisor)
```promql
# CPU por contenedor
sum(rate(container_cpu_usage_seconds_total{name!=""}[5m])) by (name) * 100

# Memoria por contenedor (MB)
container_memory_usage_bytes{name!=""} / 1024 / 1024

# Red por contenedor
rate(container_network_receive_bytes_total{name!=""}[5m])
```

## 🎯 Crear Dashboard Personalizado

1. En Grafana, click en **+** → **Dashboard** → **Add new panel**
2. En la sección **Query**, ingresa:
```promql
   rate(node_cpu_seconds_total{mode!="idle"}[5m]) * 100
```
3. Personaliza:
   - **Panel title**: Uso de CPU
   - **Unit**: Percent (0-100)
   - **Graph type**: Time series
4. Click en **Apply** → **Save dashboard**

## 🛑 Comandos Útiles

### Ver logs
```powershell
# Logs de todos los servicios
docker-compose logs -f

# Logs de un servicio específico
docker-compose logs -f prometheus
docker-compose logs -f grafana
```

### Gestión de servicios
```powershell
# Ver estado
docker-compose ps

# Reiniciar servicios
docker-compose restart

# Reiniciar un servicio específico
docker-compose restart grafana

# Detener servicios
docker-compose stop

# Detener y eliminar contenedores
docker-compose down

# Detener y eliminar contenedores + volúmenes
docker-compose down -v
```

### Ver recursos en tiempo real
```powershell
docker stats
```

## 🔍 Troubleshooting

### Problema 1: No aparecen métricas en Grafana

**Solución:**
1. Verifica que Prometheus esté recolectando datos:
   - Ve a http://localhost:9090/targets
   - Todos los targets deben estar en estado "UP" (verde)
2. Verifica la conexión del data source en Grafana
3. Revisa los logs: `docker-compose logs prometheus`

### Problema 2: No puedo acceder a Grafana

**Solución:**
```powershell
# Ver logs
docker logs grafana

# Reiniciar contenedor
docker-compose restart grafana

# Verificar que el puerto 3000 no esté en uso
netstat -ano | findstr :3000
```

### Problema 3: Docker Desktop no está corriendo

**Solución:**
1. Abre Docker Desktop
2. Espera a que inicie completamente
3. Verifica el ícono en la bandeja del sistema (debe estar verde)

### Problema 4: Puerto en uso

**Solución:**
Edita `docker-compose.yml` y cambia el puerto:
```yaml
ports:
  - "3001:3000"  # Cambiar 3000 por 3001
```

## 📸 Screenshots

Agrega capturas de pantalla en la carpeta `screenshots/`:
- Prometheus Targets
- Grafana Dashboard
- Node Exporter Metrics

## 📚 Recursos Adicionales

- [Documentación Prometheus](https://prometheus.io/docs/)
- [Documentación Grafana](https://grafana.com/docs/)
- [Galería de Dashboards](https://grafana.com/grafana/dashboards/)
- [PromQL Tutorial](https://prometheus.io/docs/prometheus/latest/querying/basics/)

## 👤 Autor

Karla - Práctica de Monitoreo con Prometheus y Grafana

---

⭐ Si este proyecto te fue útil, dale una estrella en GitHub!
