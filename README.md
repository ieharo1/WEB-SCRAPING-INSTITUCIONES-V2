# 🕷️ Web Scraping Instituciones v0.0.2 - Scrapy Version

Sistema profesional de web scraping utilizando Scrapy framework para extracción de contactos institucionales. Desarrollado por **Isaac Esteban Haro Torres**.

---

## 📝 Descripción

Implementación basada en Scrapy para scraping robusto y escalable de sitios web educativos. Ideal para proyectos grandes que requieren manejo eficiente de múltiples páginas.

### ¿Qué hace este proyecto?

- **Scrapy Spiders**: Estructura profesional de spiders
- **Pipeline de datos**: Procesamiento automático en pipeline
- **Rate limiting**: Control automático de velocidad
- **Item Loaders**: Normalización de datos extraídos
- **Exportadores**: JSON, CSV, JSONLines integrados
- **Middleware**: Proxy rotation, user-agent rotation

---

## ✨ Características Principales

| Característica | Descripción |
|----------------|-------------|
| 🕷️ **Framework Scrapy** | Arquitectura profesional |
| 📦 **Items & Pipelines** | Procesamiento estructurado |
| 🔄 **Auto-rotación** | Proxies y User-Agents |
| 💾 **Multi-export** | JSON, CSV, XML |
| ⚙️ **Configuración** | settings.py completo |
| 📊 **Stats integradas** | Métricas de scraping |

---

## 🛠️ Stack Tecnológico

- **Framework**: Scrapy
- **Python**: 3.10+
- **HTTP**: requests, aiohttp
- **Datos**: Scrapy Items, ItemLoaders
- **Proxy**: scrapy-proxies
- **Deployment**: Scrapy Cloud (opcional)

---

## 🚀 Instalación y Uso

### Instalación

```bash
pip install scrapy scrapy-user-agents scrapy-proxies
```

### Estructura del Proyecto Scrapy

```
myproject/
├── myproject/
│   ├── spiders/
│   │   └── instituciones_spider.py
│   ├── items.py
│   ├── pipelines.py
│   ├── settings.py
│   └── middlewares.py
├── scrapy.cfg
└── README.md
```

### Crear Proyecto

```bash
scrapy startproject institucion_scraper
cd institucion_scraper
```

### Ejecutar Spider

```bash
# Ejecución básica
scrapy crawl instituciones -o resultados.json

# Con archivo CSV
scrapy crawl instituciones -o resultados.csv

# Con límite de items
scrapy crawl instituciones -s CLOSESPIDER_ITEMCOUNT=100
```

---

## 📁 Estructura de Código

### Items (items.py)

```python
import scrapy
from scrapy.item import Field
from scrapy.loader import ItemLoader
from itemloaders.processors import TakeFirst, MapCompose

class InstitucionItem(scrapy.Item):
    nombre = Field()
    email = Field()
    telefono = Field()
    direccion = Field()
    provincia = Field()
    ciudad = Field()
    nivel = Field()  # primaria, secundaria, superior
    tipo = Field()   # colegio, universidad, instituto
    website = Field()
    fecha_extraccion = Field()
```

### Spider (spiders/instituciones.py)

```python
import scrapy
from ..items import InstitucionItem

class InstitucionesSpider(scrapy.Spider):
    name = 'instituciones'
    allowed_domains = ['edu.ec']
    
    def start_requests(self):
        urls = [
            'https://www.educacion.gob.ec/directorio/',
            # ...
        ]
        for url in urls:
            yield scrapy.Request(url, callback=self.parse)
    
    def parse(self, response):
        for institucion in response.css('div.institucion'):
            loader = ItemLoader(item=InstitucionItem(), selector=institucion)
            loader.add_css('nombre', 'h3.title::text')
            loader.add_css('email', 'a.email::text')
            loader.add_css('telefono', 'span.phone::text')
            yield loader.load_item()
```

### Pipeline (pipelines.py)

```python
class ValidacionPipeline:
    def process_item(self, item, spider):
        # Validar email
        if item.get('email') and '@' in item['email']:
            return item
        raise DropItem("Email inválido: {item['email']}")

class DuplicadosPipeline:
    def __init__(self):
        self.emails_vistos = set()
    
    def process_item(self, item, spider):
        if item['email'] in self.emails_vistos:
            raise DropItem("Duplicado")
        self.emails_vistos.add(item['email'])
        return item
```

---

## ⚙️ Configuración (settings.py)

```python
# settings.py
BOT_NAME = 'institucion_scraper'

ROBOTSTXT_OBEY = True

DOWNLOAD_DELAY = 2
CONCURRENT_REQUESTS_PER_DOMAIN = 1

USER_AGENTS = [
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64)...',
    'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)...',
]

DOWNLOADER_MIDDLEWARES = {
    'scrapy_user_agents.middlewares.RandomUserAgentMiddleware': 400,
    'scrapy_proxies.middlewares.RandomProxyMiddleware': 100,
}

ITEM_PIPELINES = {
    'institucion_scraper.pipelines.ValidacionPipeline': 100,
    'institucion_scraper.pipelines.DuplicadosPipeline': 200,
    'institucion_scraper.pipelines.ExportPipeline': 300,
}

FEEDS = {
    'resultados.json': {'format': 'json'},
    'resultados.csv': {'format': 'csv'},
}
```

---

## 📊 Monitoreo

```
2026-01-15 10:30:15 [scrapy.extensions.logstats] INFO: Crawled 200 pages (at 15 pages/min)
2026-01-15 10:30:15 [scrapy.extensions.logstats] INFO: [...] Itemscraped: 450
```

---

## 💡 Ventajas de Scrapy

| Característica | Benefit |
|----------------|---------|
| Asíncrono | Alta velocidad |
| selectores CSS/XPath | Flexibilidad |
| Pipelines | Procesamiento modular |
| Middleware | Extensibilidad |
| Shells | Testing rápido |
| Stats integradas | Monitoreo fácil |

---

## 📈 Comparación

| Método | Velocidad | Complejidad | Mantenibilidad |
|--------|-----------|-------------|----------------|
| BeautifulSoup | Media | Baja | Media |
| Scrapy | Alta | Media | Alta |
| Selenium | Baja | Alta | Baja |

---

## ⚠️ Mejores Prácticas

- Siempre obedece robots.txt
- Configura DOWNLOAD_DELAY
- Usa proxies para sitios grandes
- Implementa retry middleware
- Maneja errores apropiadamente

---

## 🤝 Contribuciones

¿Creaste nuevos spiders?
¡Abre un Pull Request!

---

## 👨‍💻 Desarrollado por Isaac Esteban Haro Torres

**Ingeniero en Sistemas · Full Stack · Automatización · Data**

- 📧 Email: zackharo1@gmail.com
- 📱 WhatsApp: 098805517
- 💻 GitHub: https://github.com/ieharo1
- 🌐 Portafolio: https://ieharo1.github.io/portafolio-isaac.haro/

---

© 2026 Isaac Esteban Haro Torres - Todos los derechos reservados.
