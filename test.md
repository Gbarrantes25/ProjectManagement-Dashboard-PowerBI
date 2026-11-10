# 📊 Project Management Dashboard

[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Neon](https://img.shields.io/badge/Neon-00E599?style=flat&logo=neon&logoColor=white)](https://neon.tech/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.0-blue.svg)]()

---

## 📋 Tabla de Contenidos

- [Descripción General](#-descripción-general)
- [Características Principales](#-características-principales)
- [Contenido del Proyecto](#-contenido-del-proyecto)
- [Herramientas y Tecnologías](#️-herramientas-y-tecnologías-utilizadas)
- [Configuración del Entorno](#️-configuración-del-entorno)
- [Estructura del Repositorio](#-estructura-del-repositorio)
- [Vistas Previas](#️-vistas-previas-del-proyecto)
- [Autor](#-autor)
- [Licencia](#-licencia)

---

## 📃 Descripción General

Dashboard profesional para **Project Management** desarrollado en Power BI, diseñado para centralizar y analizar los proyectos y tareas de una empresa. Este proyecto se conecta a una base de datos remota **NeonTech (PostgreSQL)** y transforma datos en información accionable mediante:

- 📊 **5 Vistas Analíticas:** Overview, Project Details, Analysis, Task Status y Schedule & Timeline
- 📈 **+20 Medidas DAX:** KPIs avanzados para gestión de proyectos (CPI, SPI, CV, Budget, AC, VAC, EAC, PV, EV, etc.)
- 🎯 **AC vs Budget:** Comparativa detallada de costos reales vs presupuesto (por proyecto y tarea)
- 📅 **Análisis Temporal:** Tendencias mensuales, acumulados YTD (Year-To-Date) y diagrama de Gantt interactivo
- 🚦 **Sistema de Alertas:** Clasificación automática de proyectos según su estado (Healthy, At Risk, Critical)

---

## ✨ Características Principales

### 🔄 Transformaciones en Power Query
- Procesos de limpieza y modelado de datos para optimizar el rendimiento
- Creación de tabla calendario personalizada
- 6 consultas SQL optimizadas para extracción de datos

### 📐 Modelo de Datos
- **Tablas de Dimensión:** Projects, Project Management, Phase, Task, Calendar
- **Tabla de Hechos:** Fact_Task con información histórica detallada
- Relaciones optimizadas para análisis multidimensional

### 🎨 Diseño Interactivo
- Navegación por páginas con paginado intuitivo
- Segmentación de datos dinámica
- Tarjetas KPI con diseño moderno y gradientes
- Visualizaciones personalizadas con HTML + DAX

---

## 📊 Contenido del Proyecto

| Página | Descripción | Contenido Principal |
|--------|-------------|---------------------|
| **Overview** | Vista general ejecutiva | KPIs globales, estado de proyectos, alertas |
| **Project Details** | Detalle por proyecto | Gastos acumulados, CPI vs SPI, Diagrama de Gantt |
| **Analysis** | Análisis profundo | Desglose por proyecto y fase, métricas de rendimiento |
| **Task Status** | Estado de tareas | Detalle por tarea, responsable, progreso |
| **Schedule & Timeline** | Análisis temporal | Comparación planificado vs real, tendencias |

---

## 🛠️ Herramientas y Tecnologías Utilizadas

### Software Requerido
- **Power BI Desktop** (versión más reciente)
- **PostgreSQL** o **DBeaver** (cliente de base de datos)
- **NeonTech** (base de datos en la nube)

### Fuente de Datos
- 📄 [projects.csv](https://github.com/Gbarrantes25/ProjectManagement-Dashboard-PowerBI/blob/main/Data%20Source/projects.csv)

### Custom Visuals
- 📊 [Power Gantt Chart](https://marketplace.microsoft.com/en-us/product/power-bi-visuals/novasilvagroupbv1626174611009.powerganttchart?tab=overview) - Visualización de diagrama de Gantt

### Lenguajes y Tecnologías
- **SQL** - Extracción y consulta de tablas
- **DAX** - Cálculo de medidas y KPIs (+20 fórmulas)
- **Power Query (Lenguaje M)** - Transformación y limpieza de datos

---

## ⚙️ Configuración del Entorno

### Prerequisitos
1. Descargar e instalar [Power BI Desktop](https://powerbi.microsoft.com/desktop/)
2. Instalar [PostgreSQL](https://www.postgresql.org/download/) o [DBeaver](https://dbeaver.io/download/)

### Paso 1: Configurar NeonTech
1. Crear cuenta en [Neon](https://neon.com/)
2. Obtener credenciales desde "Connect to your project" / "Connection String"
3. Formato esperado:
   ```
   postgresql://neondb_owner:YOURPASSWORD@ep-old-cloud-a8jxpwbf-pooler.eastus2.azure.neon.tech/neondb?sslmode=require&channel_binding=require
   ```
5. Desglosar las credenciales:
   - **Database:** neondb
   - **Server:** ep-old-cloud-a8jxpwbf-pooler.eastus2.azure.neon.tech
   - **Port:** 5432
   - **Usuario:** neondb_owner
   - **Contraseña:** (Tu contraseña del proyecto)

### Paso 2: Conectar y Crear Tabla
1. Abrir DBeaver o PgAdmin4
2. Agregar nueva conexión con las credenciales anteriores
3. Crear la tabla en el esquema público:

<details>
<summary>📄 Ver Script SQL - Crear Tabla</summary>

```sql
CREATE TABLE public.projects (
    id int4 NOT NULL,
    task_id int4 NOT NULL,
    project_id int4 NOT NULL,
    project_name text NOT NULL,
    area text NOT NULL,
    project_type text NOT NULL,
    project_management_id int4 NOT NULL,
    project_management text NOT NULL,
    task_name text NOT NULL,
    phase_id int4 NOT NULL,
    phase text NOT NULL,
    phase_weight numeric NOT NULL,
    "owner" text NOT NULL,
    planned_startdate date NOT NULL,
    planned_enddate date NOT NULL,
    real_startdate date NULL,
    real_enddate date NULL,
    budget numeric NOT NULL,
    ac numeric NULL,
    progress_acc numeric NOT NULL,
    data_date date NOT NULL,
    CONSTRAINT projects_pkey PRIMARY KEY (id)
);
```
</details>
4. Importar el archivo projects.csv en la tabla creada

### Paso 3: Conectar Power BI a PostgreSQL
Abrir Power BI Desktop y realizar 6 conexiones independientes a PostgreSQL, modificando solo la consulta SQL en cada una:

<details>
<summary>📅 DateSource (Tabla de fechas unificada)</summary>
  
```SQL
SELECT planned_startdate as "FDate" from projects where planned_startdate is not NULL
UNION ALL
SELECT planned_enddate from projects where planned_enddate is not NULL
UNION ALL
SELECT real_startdate from projects where real_startdate is not NULL
UNION ALL
SELECT real_enddate from projects where real_enddate is not NULL
ORDER BY "FDate"
```
</details>

<details>
<summary>📁 DimProjects (Dimensión de proyectos)</summary>

```SQL
SELECT DISTINCT project_id, project_name, area, project_type 
FROM projects 
ORDER BY project_id
```
</details>

<details>
<summary>👥 DimProjectManagement (Dimensión de jefes de proyecto)</summary>

```SQL
SELECT DISTINCT project_management_id, project_management 
FROM projects 
ORDER BY project_management_id
```

<details>
<summary>🔄 DimPhase (Dimensión de fases)</summary>

```SQL
SELECT DISTINCT phase_id, phase 
FROM projects 
ORDER BY phase_id
```
</details>

<details>
<summary>✅ DimTask (Dimensión de tareas)</summary>

```sql
SELECT DISTINCT task_id, task_name, project_id, planned_startdate, planned_enddate 
FROM projects 
ORDER BY task_id ASC
```

</details>

<details>
<summary>📊 Fact_Task (Tabla de hechos)</summary>

```sql
SELECT 
    Id,
    task_id,
    task_name,
    project_id,
    project_management_id,
    phase_id,
    phase_weight,
    "owner",
    planned_startdate,
    planned_enddate,
    real_startdate,
    real_enddate,
    budget,
    AC,
    Progress_Acc,
    Data_Date
FROM projects
```

</details>

## 📂 Estructura del Repositorio

```code
.
├── Data Source/                    # Contiene datos fuente
│   └── projects.csv               # Archivo CSV con datos del proyecto
├── Project Management.Report/     # Componentes del reporte (formato .pbip)
├── Project Management.SemanticModel/  # Modelo semántico
├── Project Management.pbip        # Proyecto Power BI (formato nuevo)
├── Project Management.pbix        # Archivo ejecutable de Power BI
├── .gitignore                     # Archivo de configuración Git
└── README.md                      # Este archivo
```

## 🧮 Medidas DAX Implementadas
sadsa
