# MCP UJI Academic Server

Servidor MCP (Model Context Protocol) completo que proporciona acceso a la información académica de la Universitat Jaume I (UJI). Soporta tanto modo local (stdio) como remoto (HTTP/WebSocket) para máxima flexibilidad de despliegue.

## ✨ Características Principales

- 🎓 **Acceso Completo a Datos Académicos**: Asignaturas, titulaciones, horarios y ubicaciones
- 🌐 **Soporte Multiidioma**: Contenido en catalán, español e inglés  
- 🔄 **Modo Dual**: Local (stdio) para Claude Desktop local y remoto (HTTP/WebSocket) para acceso de red
- ⚡ **Cache Inteligente**: Sistema de caché integrado para mejor rendimiento
- 🔍 **Funcionalidad de Búsqueda**: Búsqueda avanzada en asignaturas, titulaciones y ubicaciones
- 📅 **Gestión de Horarios**: Análisis y gestión de horarios en formato iCalendar
- 🛡️ **Manejo Robusto de Errores**: Gestión de errores con mensajes descriptivos
- 🔒 **Seguridad de Tipos**: Type hints completos y modelos Pydantic para validación

## 🔧 Instalación

### Prerrequisitos

- Python 3.12 o superior
- Gestor de paquetes UV

### Configuración

1. **Clona el repositorio**:

   ```bash
   git clone <repository-url>
   cd MCP_UJI_academic
   ```

2. **Instala las dependencias con UV**:

   ```bash
   uv sync
   ```

## 🚀 Uso del Servidor

### Modo Local (stdio) - Para Claude Desktop Local

```bash
# Opción 1: Usar el launcher
python start_server.py --mode local

# Opción 2: Directamente
uv run server.py
```

### Modo Remoto (HTTP/WebSocket) - Para Acceso de Red

```bash
# Servidor en localhost (desarrollo)
python start_server.py --mode remote --host 127.0.0.1 --port 8084

# Servidor accesible desde la red (para servidor de prueba 150.128.81.57)
python start_server.py --mode remote --host 0.0.0.0 --port 8084

# Con auto-reload para desarrollo
python start_server.py --mode remote --host 127.0.0.1 --port 8084 --reload
```

> **💡 Nota para servidor de prueba**: Para ejecutar en el servidor de prueba (150.128.81.57), usa `--host 0.0.0.0` para permitir conexiones desde cualquier IP de la red.

## 📋 Configuración en Claude Desktop

### Para Modo Local (stdio)

Añade a tu configuración de Claude Desktop (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "mcp-uji-academic": {
      "command": "uv",
      "args": ["run", "/ruta/completa/al/proyecto/MCP_UJI_academic/server.py"],
      "description": "UJI Academic Server - Local Mode"
    }
  }
}
```

### Para Modo Remoto (WebSocket)

**Localhost (desarrollo local):**

```json
{
  "mcpServers": {
    "mcp-uji-academic-remote": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/client-websocket", "ws://localhost:8084/ws/claude-desktop"],
      "description": "UJI Academic Server - Remote Mode (localhost)"
    }
  }
}
```

**Servidor de prueba (IP específica):**

```json
{
  "mcpServers": {
    "mcp-uji-academic-remote-test": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/client-websocket", "ws://150.128.81.57:8084/ws/claude-desktop"],
      "description": "UJI Academic Server - Remote Mode (servidor de prueba)"
    }
  }
}
```

## 🌐 Endpoints del Servidor Remoto

### Desarrollo Local (localhost)

- 🏠 **Página principal**: `http://localhost:8084/`  
- 💓 **Health check**: `http://localhost:8084/health`
- 🛠️ **Lista de herramientas**: `http://localhost:8084/tools`
- 🔌 **WebSocket MCP**: `ws://localhost:8084/ws/{client_id}`

### Servidor de Prueba (150.128.81.57)

- 🏠 **Página principal**: `http://150.128.81.57:8084/`  
- 💓 **Health check**: `http://150.128.81.57:8084/health`
- 🛠️ **Lista de herramientas**: `http://150.128.81.57:8084/tools`
- 🔌 **WebSocket MCP**: `ws://150.128.81.57:8084/ws/{client_id}`

## 🛠️ Herramientas MCP Disponibles

### Asignaturas

- **`get_subjects`**: Obtener lista paginada de asignaturas
- **`search_subjects`**: Buscar asignaturas por nombre o ID
- **`get_subject_details`**: Detalles completos de una asignatura

### Titulaciones  

- **`get_degrees`**: Obtener lista de titulaciones
- **`search_degrees`**: Buscar titulaciones por nombre
- **`get_degree_details`**: Detalles de una titulación específica

### Ubicaciones

- **`get_locations`**: Obtener ubicaciones universitarias
- **`search_locations`**: Buscar ubicaciones por nombre

### Horarios

- **`get_class_schedule`**: Horarios de clases por titulación y año
- **`get_exam_schedule`**: Horarios de exámenes por titulación y año

## 📚 Recursos MCP

- **`uji://api/info`**: Información sobre la API y endpoints disponibles

## 🧪 Desarrollo y Testing

### Ejecutar Tests

```bash
# Tests de integración
uv run python integration_test.py

# Test específico del servidor
uv run python test_server.py
```

### Desarrollo con Auto-reload

```bash
python start_server.py --mode remote --reload
```

### Verificar Servidor Remoto

**Desarrollo local:**

```bash
# Health check
curl http://localhost:8084/health

# Lista de herramientas disponibles  
curl http://localhost:8084/tools
```

**Servidor de prueba:**

```bash
# Health check
curl http://150.128.81.57:8084/health

# Lista de herramientas disponibles  
curl http://150.128.81.57:8084/tools
```

## 🔗 API Externa Utilizada

Este servidor utiliza la API REST oficial de UJI:

- **Base URL**: https://ujiapps.uji.es/lod-autorest/api/
- **Datasets**: asignaturas, titulaciones, ubicaciones, horarios
- **Formatos**: JSON, iCalendar (para horarios)

## 📁 Estructura del Proyecto

```
MCP_UJI_academic/
├── server.py              # Servidor MCP principal (modo local)
├── remote_server.py       # Servidor HTTP/WebSocket (modo remoto)  
├── start_server.py        # Launcher para ambos modos
├── api_client.py          # Cliente API de UJI
├── models.py              # Modelos Pydantic
├── integration_test.py    # Tests de integración
├── test_server.py         # Tests del servidor
├── claude_desktop_config.json  # Ejemplo configuración Claude
├── pyproject.toml         # Configuración del proyecto
└── README.md              # Esta documentación
```

## 🔧 Solución de Problemas

### Error "Port already in use"

```bash
# Verificar qué proceso usa el puerto
lsof -i :8084

# Usar otro puerto
python start_server.py --mode remote --port 8001
```

### Error de conexión WebSocket

- Verifica que el servidor remoto esté ejecutándose
- Comprueba la URL del WebSocket en la configuración
- Revisa los logs del servidor para errores

### Conexión al servidor de prueba (150.128.81.57)

Si no puedes conectarte al servidor de prueba:

```bash
# Verificar conectividad de red
ping 150.128.81.57

# Verificar que el puerto 8084 esté accesible
telnet 150.128.81.57 8084
# o con nc:
nc -zv 150.128.81.57 8084
```

**Configuración de firewall** (en el servidor de prueba):

```bash
# Permitir tráfico en puerto 8084
sudo ufw allow 8084/tcp

# Verificar estado del firewall
sudo ufw status
```

### Problemas con uv

```bash
# Reinstalar dependencias
uv sync --reinstall
```

## 🚦 Estado del Proyecto

- ✅ Servidor local (stdio) funcional
- ✅ Servidor remoto (HTTP/WebSocket) funcional  
- ✅ 8 herramientas MCP implementadas
- ✅ Tests de integración completos
- ✅ Documentación completa
- ✅ Ejemplos de configuración

## 📄 Licencia

MIT License

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor:

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📞 Soporte

Si encuentras algún problema o tienes preguntas:

1. Revisa la sección de solución de problemas
2. Ejecuta los tests de integración
3. Verifica la configuración de Claude Desktop
4. Crea un issue en el repositorio