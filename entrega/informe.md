# 📄 Informe Técnico del Taller

## 🔖 Nombre del Taller
Taller 3 - Arquitectura Actual del Sistema con el Modelo C4

## 👥 Integrantes del equipo
- Nicolás Clavijo
- Mauricio Suárez

## 🧠 Descripción general del trabajo
El objetivo de este taller fue representar la arquitectura actual del sistema de nuestro cliente real, **Asul Tecnologías de la Información SAS**, usando las vistas C1 (Contexto) y C2 (Contenedores) del modelo C4. Seguimos la misma metodología de 4 pasos que aplicamos en clase al caso base de RedExpress.

El trabajo tuvo dos momentos:
1. **Primera versión (supuesta):** partimos de la Ficha de Caracterización del Cliente, diligenciada con el equipo (Nexio SAS). Como la ficha describe procesos de negocio y no un sistema técnico concreto, inferimos una plataforma genérica: portal del cliente, bus SOA y módulo de calidad.
2. **Versión actual (validada con el cliente):** hicimos una entrevista con Asul sobre su ecosistema de desarrollo, soporte, seguridad e infraestructura. Con esa información rehicimos los dos diagramas y este informe para que representen **cómo trabaja Asul hoy** y no una arquitectura supuesta.

## 🔧 Proceso de desarrollo
1. **Revisión de la ficha de caracterización.** Asul es una empresa de Chía (Cundinamarca) que hace desarrollo de software a la medida y soporte técnico bajo modelos de calidad certificados (CMMI, MPS.Br, ITMark, ISO/IEC 29110). Rentec es uno de sus clientes estratégicos.
2. **Entrevista con el cliente.** Preguntamos por las herramientas, los usuarios y roles, el flujo de tickets, la aprobación de requisitos, el alojamiento de los ambientes, los respaldos, la trazabilidad, la seguridad de la información y la protección de datos.
3. **Redefinición del sistema en alcance.** Según la entrevista, lo que Asul opera y controla no es una plataforma a la medida sino un **ecosistema de herramientas** con el que gestiona sus proyectos y el soporte: Azure DevOps, GitHub y SharePoint. Por eso ese ecosistema es la caja central del C1. Jira, el correo, Teams, Entra ID, los ambientes en Azure y el SOC de Rentec quedan como sistemas externos, porque son de terceros o del cliente.
4. **C1 – Contexto.** Identificamos los actores reales (líder técnico, analista de sistemas, equipo de desarrollo y pruebas de Asul, mesa de primer nivel de SBS y el cliente Rentec como aprobador) y etiquetamos cada relación con lo que se hace y cómo.
5. **C2 – Contenedores.** Descompusimos el ecosistema en Azure Boards y Queries, la extensión Time Tracker, el repositorio en GitHub y la gestión documental en SharePoint. Como infraestructura de soporte agregamos el equipo de respaldo local y los discos de backup.
6. **Validación.** Construimos los diagramas en draw.io con la leyenda de colores de la [guía paso a paso](../clase/guia_paso_a_paso_c4.md) y los revisamos contra la checklist de autoevaluación.

## 🧩 Análisis del modelo propuesto

### Estructura del modelo
- **C1:** el *Ecosistema de Gestión de Desarrollo y Soporte de Asul* aparece como una sola caja. A su alrededor están tres actores internos de Asul, dos actores del lado del cliente y seis sistemas externos. Un detalle importante es que **Jira no se conecta con el ecosistema**: entre los dos solo hay personas y correo, porque hoy no existe ninguna integración automática.
- **C2:** cada contenedor tiene una responsabilidad distinta:
  - *Boards/Queries:* trabajo y casos de soporte.
  - *Time Tracker:* tiempos por actividad.
  - *GitHub:* código fuente.
  - *SharePoint:* documentos con líneas base.

  La infraestructura de respaldo se muestra aparte porque es un punto crítico de la operación.

### Flujo principal: de un ticket en Jira a un caso en Azure DevOps
1. El cliente final de Rentec (SBS) recibe una solicitud. Su **mesa de primer nivel** crea el ticket en el **Jira de Rentec** y lo analiza.
2. Si el ticket debe atenderlo Asul, una persona de SBS lo **asigna a Rentec**. Esa asignación la ve Asul, que trabaja dentro de ese Jira con una **única cuenta compartida**, la del líder técnico.
3. Jira envía un **correo electrónico** cada vez que un ticket es asignado, mencionado o movido.
4. Con ese correo, el líder técnico o el analista revisan el ticket y **transcriben manualmente** el caso en **Azure Boards**. Allí lo diagnostican, lo resuelven y le cambian el estado. Si el correo no llega, Asul no se entera del movimiento.
5. El equipo registra el tiempo dedicado con **Time Tracker**.

### Flujo de requisitos y estimaciones
El documento de requisitos se escribe en Word y se versiona en SharePoint: **borradores** 1.1, 1.2… y **líneas base** 1.0, 2.0 en cada entrega. Luego se exporta a PDF y se envía por correo. El cliente lo aprueba **respondiendo ese correo**, y las estimaciones de horas se aprueban igual. No se usa firma digital. Las líneas base **no quedan bloqueadas**, pero SharePoint guarda la trazabilidad de los cambios.

### Flujo de despliegue
No se usan Azure Pipelines ni otra herramienta de CI/CD: **el despliegue es manual**. Por lo general lo hace el líder técnico. Para contingencias se capacitó a otro miembro del equipo y hay un tercero que lo acompaña.

Los ambientes (pruebas y producción) están **solo en Microsoft Azure**, sobre máquinas virtuales y **Azure SQL**. Hay replicación entre **East US y West US**, con pruebas de recuperación ante desastres. La base de datos tiene dos réplicas: una para reportes, que no afecta a la productiva, y otra en espera. La infraestructura se monitorea con **Azure Monitor**.

Asul no tiene infraestructura propia on-premise, y cada desarrollador trabaja en su propio computador. Para SBS, Asul administra la producción, pero el acceso está restringido por IP y por VPN (VPN a la que solo accede el cliente). Para otros clientes, Asul aloja y administra el 100 % del ambiente.

### Cómo representa las necesidades del cliente
El modelo deja a la vista lo que Asul necesita cuidar para mantener tiempos de respuesta rápidos y sus estándares de calidad: dónde se pierde tiempo (la transcripción manual), dónde se pierde trazabilidad (la cuenta compartida) y dónde está el riesgo operativo (despliegues y respaldos).

### Supuestos de la versión anterior que la entrevista corrigió

| Supuesto inicial | Lo que se encontró en la entrevista |
|---|---|
| Portal del Cliente propio (ASP.NET MVC) donde el cliente reporta incidentes | Los tickets entran por el **Jira de Rentec**; Asul no tiene portal propio |
| Bus de Integración SOA (ESB) que integra con los sistemas del cliente | **No hay integración**: el paso de Jira a Azure DevOps es 100 % manual |
| Consola de Gestión de Proyectos .NET a la medida | Se usan **Azure Boards y Queries** (SaaS) |
| Base de datos corporativa en SQL Server | Los datos de gestión viven en Azure DevOps y SharePoint; **Azure SQL** es la base de las aplicaciones de los clientes |
| Organismo certificador como sistema externo conectado | No hay intercambio de sistemas con certificadores. Se aplican controles de ISO 27000 por medio de ITMark, **sin certificación ISO 27001** |

### Supuestos que se mantienen y quedan pendientes de validar
- El detalle técnico de las máquinas virtuales y la región principal (East o West US): la persona entrevistada no lo conocía.
- La política de copias de seguridad del código y las pruebas de restauración: se recomendó validarlo con el líder técnico.
- Si el cliente exige cumplir alguna circular del sector financiero o asegurador como condición del contrato: la pregunta quedó sin respuesta.

## ⚠️ Fortalezas y debilidades actuales

| # | Debilidad | Impacto | Elemento del modelo |
|---|---|---|---|
| D1 | Transcripción **manual** de Jira a Azure DevOps, disparada por correo | Retrabajo; si no llega el correo se pierde el movimiento del ticket | Relación Correo → Líder/Analista → Boards |
| D2 | **Una sola cuenta compartida** en Jira (el cliente solo dio una licencia) | Se pierde la trazabilidad por persona y el control de acceso individual | Relación Líder Técnico → Jira |
| D3 | **Despliegue manual sin CI/CD**, concentrado en una persona | Punto único de falla y errores humanos en producción | Relación Líder Técnico → Ambientes Azure |
| D4 | El respaldo de SharePoint depende de **un PC encendido**; la copia a otro directorio es solo **semanal** | Pérdida de hasta una semana de documentos si el PC falla | Equipo de Respaldo Local / Discos de Backup |
| D5 | **No hay plan de contingencia** si Azure DevOps o SharePoint se caen | Operación paralizada durante la caída | Contenedores Boards y SharePoint |
| D6 | Aprobación de requisitos **por correo**, sin firma digital, y líneas base no bloqueadas | Evidencia débil ante un desacuerdo | Relación SharePoint → Correo |
| D7 | Código fuente conservado **indefinidamente**, sin procedimiento de eliminación ni control técnico contra su descarga (solo cláusulas y políticas) | Riesgo de fuga y de incumplimiento de la minimización de datos | Repositorio GitHub / Discos de Backup |
| D8 | Sin certificación externa ISO 27001 | Menor evidencia formal de seguridad ante clientes del sector financiero y asegurador | Transversal |

**Fortalezas encontradas:**
- Usuarios de Azure DevOps integrados con el directorio activo (Microsoft Entra ID) y **MFA** en todas las cuentas de Microsoft.
- Permisos por **roles** (Administrador / Contributor) organizados por equipos.
- **Historial** de cambios en cada work item y en Jira.
- Replicación entre regiones con **pruebas de DR** y réplicas de base de datos.
- **Ofuscación** de los datos de producción cuando se copian a pruebas.
- **SOC** del cliente que alerta por IPs no reconocidas y picos de solicitudes.
- Cláusulas de confidencialidad.
- Política de **habeas data** publicada (Ley 1581 de 2012), con procedimiento para rectificar o revocar.
- **Auditorías semestrales** de seguridad de la información a los empleados: contraseñas, escritorio limpio, correo limpio.
- No se reportaron incidentes de seguridad en los últimos años.

## 📈 Diagrama final entregado
- Vista de Contexto (C1): [`entrega/c1-contexto-final.drawio`](./c1-contexto-final.drawio)
- Vista de Contenedores (C2): [`entrega/c2-contenedores-final.drawio`](./c2-contenedores-final.drawio)

> Ábralos en [draw.io / diagrams.net](https://app.diagrams.net/).

## 📋 Tabla de actores, entidades o componentes

| Nombre del elemento | Tipo | Descripción | Responsable |
|---|---|---|---|
| Líder Técnico de Asul | Actor | Gestiona los casos de soporte, usa la cuenta compartida de Jira y, por lo general, despliega a producción | Asul |
| Analista de Sistemas de Asul | Actor | Apoya al líder técnico en la gestión, el diagnóstico y los cambios de estado de los casos | Asul |
| Equipo de Desarrollo y Pruebas de Asul | Actor | Desarrolladores y testers (menos de 10 personas) que gestionan requerimientos, desarrollo, pruebas y tareas | Asul |
| Mesa de Primer Nivel de SBS | Actor | Cliente final de Rentec; crea y analiza los tickets y los escala a Rentec | SBS |
| Cliente Rentec | Actor | Cliente de Asul; aprueba por correo los documentos de requisitos y las estimaciones | Rentec |
| Ecosistema de Gestión de Desarrollo y Soporte de Asul | Sistema en alcance | Azure DevOps, GitHub y SharePoint usados para gestionar proyectos y soporte | Asul |
| Jira de Rentec | Sistema externo (SaaS) | Mesa de tickets del cliente; Asul la usa con una única cuenta | Rentec |
| Correo Electrónico (Microsoft 365) | Sistema externo (SaaS) | Notificaciones de Jira y aprobaciones formales | Microsoft / Asul |
| Microsoft Teams | Sistema externo (SaaS) | Reuniones, chat, informes y reportes de gestión | Microsoft / Asul |
| Microsoft Entra ID | Sistema externo (SaaS) | Directorio activo del dominio de Asul con MFA | Microsoft / Asul |
| Ambientes de los Clientes en Azure | Sistema externo (IaaS/PaaS) | VMs y Azure SQL en East/West US con replicación, DR y Azure Monitor | Asul (administra) / Cliente |
| SOC del Proveedor de Ciberseguridad | Sistema externo | Monitorea accesos y picos de solicitudes y alerta por correo | Proveedor de Rentec |
| Azure Boards y Queries | Contenedor (Azure DevOps Services) | Work items de proyectos y casos de soporte, con historial y roles | Asul |
| Time Tracker | Contenedor (extensión de Azure DevOps) | Registro de tiempos por actividad (iniciar / pausar / detener) | Asul |
| Repositorio de Código Fuente | Contenedor (GitHub) | Código de los proyectos de los clientes | Asul |
| Gestión Documental | Contenedor (SharePoint Online) | Requisitos y estimaciones con borradores y líneas base | Asul |
| Equipo de Respaldo Local | Infraestructura (PC como servidor) | Sincroniza SharePoint a una carpeta local todos los días, si está encendido | Asul |
| Discos de Backup | Infraestructura (almacenamiento externo) | Copia semanal de documentos y código fuente histórico | Asul |

## 🔍 Investigación complementaria

### Tema investigado:
Integración y automatización en Azure DevOps, controles de seguridad de la información (ISO/IEC 27001, MFA) y protección de datos personales en Colombia, aplicados a las debilidades encontradas en el modelo C4 de Asul.

### Resumen:
**Azure Boards** es el servicio de Azure DevOps para planear y hacer seguimiento del trabajo con *work items*, tableros Kanban, backlogs y consultas personalizadas (*queries*). Cada work item guarda su historial de cambios [7]. Esto confirma lo que dijo el cliente sobre la trazabilidad, y también explica la debilidad D1: como los tickets nacen en Jira y no en Boards, esa trazabilidad empieza tarde y depende de una transcripción manual. En la misma suite, **Azure Pipelines** automatiza la compilación, las pruebas y el despliegue continuos (CI/CD) hacia cualquier destino, incluidas máquinas virtuales en Azure [8]. Asul ya tiene la licencia de Azure DevOps, así que adoptar Pipelines es la forma más directa de atacar la debilidad D3: quita la dependencia de una sola persona y deja evidencia de qué versión se desplegó y cuándo.

En seguridad, Asul usa **Microsoft Entra ID con MFA**, que exige un segundo factor además de la contraseña y reduce mucho el riesgo de robo de credenciales [9]. Aun así, la cuenta compartida de Jira (D2) anula el beneficio de la identidad individual: con MFA, varias personas siguen usando el mismo usuario. Por otro lado, Asul aplica controles de la familia ISO 27000 por medio de ITMark, cuyo componente de gobierno de la seguridad de la información se basa en esa norma [3][4]. Sin embargo, no tiene certificación **ISO/IEC 27001** [10], el estándar que auditan formalmente los clientes del sector financiero y asegurador.

En protección de datos, la **Ley 1581 de 2012** obliga a los responsables del tratamiento a tener una política de tratamiento de datos personales y a garantizar los derechos de conocer, actualizar, rectificar y suprimir [11]. Asul la cumple para sus empleados con su política de habeas data. La retención indefinida del código fuente y de los documentos de los clientes (D7), en cambio, choca con el principio de finalidad de la ley cuando esos artefactos contienen datos personales. La ofuscación de datos al pasarlos a pruebas es una buena práctica en esa dirección.

Por último, el **modelo C4** propone que la vista de contexto muestre el sistema, las personas que lo usan y los sistemas con los que se relaciona, y que la vista de contenedores muestre las unidades desplegables y los almacenes de datos junto con sus tecnologías [5][12]. En el caso de Asul, esa separación permitió ver algo que la ficha de caracterización no mostraba: la frontera entre lo que Asul controla (su ecosistema de herramientas) y lo que depende del cliente (Jira, la VPN y el SOC). Y es justo en esa frontera donde están la mayoría de las debilidades.

## 📚 Referencias
Ver el listado completo con formato IEEE en [`referencias.md`](./referencias.md).

---

_Este documento hace parte de la entrega del Taller 3 del curso AREM (Arquitectura Empresarial) - Universidad de La Sabana._
