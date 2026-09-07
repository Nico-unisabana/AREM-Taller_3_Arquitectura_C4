# 📄 Informe Técnico del Taller

## 🔖 Nombre del Taller
Taller 3 - Arquitectura Actual del Sistema con el Modelo C4

## 👥 Integrantes del equipo
- Nicolás Clavijo
- Mauricio Suárez

## 🧠 Descripción general del trabajo
El objetivo de este taller fue representar la arquitectura actual del sistema de nuestro cliente real, **Asul Tecnologías de la Información SAS**, usando las vistas C1 (Contexto) y C2 (Contenedores) del modelo C4, siguiendo la misma metodología de 4 pasos aplicada en clase sobre el caso base de RedExpress. Partimos de la Ficha de Caracterización del Cliente diligenciada con el equipo (Nexio SAS) y, a partir de los procesos de negocio ahí descritos, sintetizamos y modelamos la plataforma tecnológica que los soporta.

## 🔧 Proceso de desarrollo
1. Revisamos la Ficha de Caracterización del Cliente: Asul es una empresa de Chía (Cundinamarca) dedicada al desarrollo de software a la medida y soporte técnico bajo modelos de calidad certificados (CMMI, MPS.Br, ITMark, ISO/IEC 29110), con arquitectura orientada a servicios (SOA) sobre .NET, y clientes estratégicos como Rentec, Compucom, Aqua, Avidanti, Tecnalia Colombia, Keypport, SoftManagement y Fisla.
2. Como la ficha describe procesos de negocio y no un sistema técnico ya nombrado (a diferencia del caso RedExpress), definimos como sistema en alcance una **Plataforma de Gestión de Proyectos y Soporte de Asul** que cubre los 3 procesos clave identificados: (a) desarrollo de software a la medida y soporte técnico, (b) gestión de proyectos con metodologías certificadas, y (c) relacionamiento con clientes estratégicos vía integración SOA.
3. Aplicamos los 4 pasos de la vista de Contexto (C1): identificar actores (Cliente Estratégico, Consultor/Desarrollador de Asul, Gerente de Proyecto), definir el sistema en alcance como una sola caja, ubicar los sistemas externos (sistemas del cliente estratégico, organismo certificador de calidad) y etiquetar cada relación.
4. Descompusimos la plataforma en la vista de Contenedores (C2): Portal del Cliente, Consola de Gestión de Proyectos, Bus de Integración SOA, Módulo de Gestión de Desarrollo a la Medida, Módulo de Soporte Técnico, Módulo de Gestión de Calidad y Cumplimiento, y una Base de Datos Corporativa (SQL Server) como infraestructura de soporte.
5. Construimos ambos diagramas en draw.io siguiendo la leyenda de notación y colores de la [guía paso a paso](../clase/guia_paso_a_paso_c4.md), y los revisamos contra la checklist de autoevaluación del taller.

## 🧩 Análisis del modelo propuesto
- **Estructura del modelo:** cada contenedor del C2 se mapea directamente a uno de los 3 procesos clave de la ficha, evitando el error común de crear "un contenedor que hace de todo": el desarrollo a la medida y el soporte técnico quedan en módulos separados aunque comparten la misma base de datos; la gestión de proyectos certificada se refleja en la Consola de Gestión de Proyectos y en el Módulo de Gestión de Calidad y Cumplimiento; y el relacionamiento con clientes estratégicos vía SOA se representa con el Bus de Integración y el Portal del Cliente.
- **Representación de las necesidades del cliente:** la ficha indica que Asul espera que la solución preserve tiempos de respuesta rápidos y sus estándares de cumplimiento (CMMI, MPS.Br, ITMark, ISO/IEC 29110). Por eso el modelo aísla la trazabilidad de calidad en un módulo propio que dialoga con un organismo certificador externo, en vez de mezclar esa lógica dentro de los módulos operativos.
- **Supuestos tomados:** la ficha de caracterización describe procesos de negocio pero no nombra un sistema técnico concreto (a diferencia de RedExpress). Por lo tanto, inferimos una arquitectura plausible y coherente con la tecnología declarada (.NET, SOA) y con los procesos descritos, en lugar de datos verificados directamente con el cliente. Este supuesto debe validarse con el cliente real antes de tomarlo como definitivo.

## 📈 Diagrama final entregado
- Vista de Contexto (C1): [`entrega/c1-contexto-final.drawio`](./c1-contexto-final.drawio)
- Vista de Contenedores (C2): [`entrega/c2-contenedores-final.drawio`](./c2-contenedores-final.drawio)

> Ábralos en [draw.io / diagrams.net](https://app.diagrams.net/).

## 📋 Tabla de actores, entidades o componentes

| Nombre del elemento | Tipo | Descripción | Responsable |
|---|---|---|---|
| Cliente Estratégico | Actor | Empresa cliente (Rentec, Compucom, Aqua, Avidanti, Tecnalia, Keypport, SoftManagement, Fisla) que solicita desarrollos y reporta incidentes | Cliente |
| Consultor / Desarrollador de Asul | Actor | Ejecuta el desarrollo a la medida y atiende el soporte técnico | Asul |
| Gerente de Proyecto | Actor | Planifica los proyectos y hace seguimiento a indicadores | Asul |
| Sistemas de Información del Cliente Estratégico | Sistema externo | Sistemas del cliente con los que se integra la solución desplegada | Cliente (tercero) |
| Organismo Certificador de Calidad | Sistema externo | Entidad que evalúa el cumplimiento CMMI / MPS.Br / ISO 29110 / ITMark | Tercero |
| Portal del Cliente | Contenedor (Portal Web - ASP.NET MVC) | Punto de entrada para que el cliente solicite desarrollos y reporte incidentes | Asul |
| Consola de Gestión de Proyectos | Contenedor (Aplicación Web - .NET) | Herramienta interna de planificación y seguimiento de proyectos | Asul |
| Bus de Integración SOA | Contenedor (ESB - SOAP/REST) | Orquesta la comunicación entre módulos internos y sistemas externos | Asul |
| Módulo de Gestión de Desarrollo a la Medida | Contenedor (Servicio SOA - .NET) | Gestiona el ciclo de vida del desarrollo a la medida | Asul |
| Módulo de Soporte Técnico | Contenedor (Servicio SOA - .NET) | Mesa de ayuda para incidentes de soporte | Asul |
| Módulo de Gestión de Calidad y Cumplimiento | Contenedor (Servicio SOA - .NET) | Registra evidencias para las certificaciones de calidad | Asul |
| Base de Datos Corporativa | Infraestructura (SQL Server) | Persiste proyectos, tickets y evidencias de cumplimiento | Asul |

## 🔍 Investigación complementaria

### Tema investigado:
Marcos de calidad del cliente (CMMI, ISO/IEC 29110, ITMark) y su relación con una arquitectura orientada a servicios (SOA), y su correspondencia con las vistas del modelo C4.

### Resumen:
Asul basa su propuesta de valor en tres certificaciones de calidad de naturaleza distinta. **CMMI** (Capability Maturity Model Integration) es un conjunto de buenas prácticas de referencia mundial, originalmente desarrollado para el Departamento de Defensa de EE. UU., que hoy se usa en cualquier industria para identificar brechas de capacidad y mejorar el desempeño organizacional, sin imponer niveles de madurez rígidos sino vistas personalizables según la necesidad del negocio [1]. **ISO/IEC 29110** es un estándar internacional pensado específicamente para "Very Small Entities" (VSE) — empresas o unidades muy pequeñas de desarrollo de software — que define perfiles de ciclo de vida adaptados a su tamaño, en lugar de exigirles el mismo aparato documental que a una gran corporación [2]. **ITMark**, por su parte, es una certificación europea de excelencia para pymes TIC, avalada por el European Software Institute (ESI), que evalúa a la empresa en tres frentes — gestión del negocio, ingeniería de software/sistemas y gobierno de la seguridad de la información — y que exige una revisión al año y una re-certificación completa a los tres años [3][4]. Es interesante notar que **Tecnalia** —la organización detrás de ITMark— es a la vez uno de los clientes estratégicos de Asul según la ficha de caracterización, lo que sugiere que la relación de Asul con estos marcos de calidad no es solo normativa sino también comercial.

Estos tres marcos justifican por qué, en nuestro modelo C2, separamos la lógica de cumplimiento en un contenedor propio (Módulo de Gestión de Calidad y Cumplimiento) en vez de dispersarla dentro de los módulos operativos: un auditor de CMMI, ISO 29110 o ITMark necesita evidencia trazable y centralizada, no repartida entre servicios que no fueron diseñados para eso. En cuanto a la arquitectura, el **modelo C4** —creado por Simon Brown— es un framework de visualización jerárquica en el que la vista de Contexto (C1) da una panorámica de alto nivel apta para audiencias no técnicas, mientras que la vista de Contenedores (C2) desglosa las aplicaciones, servicios y almacenes de datos del sistema y cómo se comunican, revelando ya decisiones tecnológicas concretas [5]. Que Asul use explícitamente una **arquitectura orientada a servicios (SOA)** encaja de forma natural con el C2: un bus de integración (ESB) que expone y orquesta servicios internos es, en esencia, el patrón SOA clásico aplicado a nivel de contenedor, y es el mecanismo típico para que una plataforma interna se conecte con sistemas de terceros (los clientes estratégicos) sin acoplarse directamente a su tecnología [6].

## 📚 Referencias
Ver el listado completo con formato IEEE en [`referencias.md`](./referencias.md).

---

_Este documento hace parte de la entrega del Taller 3 del curso AREM (Arquitectura Empresarial) - Universidad de La Sabana._
