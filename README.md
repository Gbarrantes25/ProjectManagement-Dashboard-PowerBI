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
</details>
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

```
.
├── Data Source/                      # Contiene datos fuente
│   └── projects.csv                  # Archivo CSV con datos del proyecto
├── Project Management.Report/        # Componentes del reporte (formato .pbip)
├── Project Management.SemanticModel/ # Modelo semántico
├── Project Management.pbip           # Proyecto Power BI (formato nuevo)
├── Project Management.pbix           # Archivo ejecutable de Power BI
├── .gitignore                        # Archivo de configuración Git
└── README.md                         # Este archivo
```

## 🧮 Medidas DAX Implementadas
#### 📊 Basics (Métricas Básicas)

<details>
<summary>Ver medidas básicas</summary>


#CountTask - Conteo de tareas únicas
```dax
=DISTINCTCOUNT(Fact_Task[task_id])
```

#ProjectsQuantity - Cantidad de proyectos


```dax
=DISTINCTCOUNT(Fact_Task[project_id])
```

#CountDone - Tareas completadas


```dax
=COUNTROWS(FILTER(
    ADDCOLUMNS(
        SUMMARIZE(Fact_Task, DimTask[task_id]),
        "Status1", [Status]
    ),[Status1]="Done")
)
```

#CountInProgress - Tareas en progreso


```dax
=COUNTROWS(FILTER(
    ADDCOLUMNS(
        SUMMARIZE(Fact_Task, DimTask[task_id]),
        "Status1", [Status]
    ),[Status1]="In Progress")
)
```

#CountToDo - Tareas pendientes


```dax
=COUNTROWS(FILTER(
    ADDCOLUMNS(
        SUMMARIZE(Fact_Task, DimTask[task_id]),
        "Status1", [Status]
    ),[Status1]="To Do")
)
```
</details>

#### 🔢 Calculations (Cálculos Financieros y de Valor)

<details>
<summary>Ver cálculos avanzados</summary>


#AC - Actual Cost (Costo Real)
```dax
=SUM(Fact_Task[ac])
```

#BU - Budget (Presupuesto)

```dax
VAR _Result = SUMX(VALUES(Fact_Task[task_id]),CALCULATE(MAX(Fact_Task[budget])))
RETURN _Result
```

#CV - Cost Variance (Variación de Costo)

```dax
=[#EV]-[#AC]
```

#EAC - Estimate At Completion (Estimación al Completar)

```dax
=IF(
    ISBLANK([#CPI]) || [#CPI] = 0,
    [#BU],
    DIVIDE([#BU], [#CPI])
)
```

#EV - Earned Value (Valor Ganado)

```dax
=[#BU]*[#Progress(%)]
```

#Executed_Budget - Presupuesto Ejecutado (%)

```dax
=DIVIDE([#AC],[#BU],0)
```

#Progress(%) - Progreso Ponderado

```dax
VAR _RealProgress = SUMX(VALUES(Fact_Task[task_id]),CALCULATE(MAX(Fact_Task[phase_weight])*CALCULATE(MAX(Fact_Task[progress_acc]))))
VAR _TotalProgress = SUMX(VALUES(Fact_Task[task_id]),CALCULATE(MAX(Fact_Task[phase_weight])))
RETURN DIVIDE(_RealProgress,_TotalProgress,0)
```

#PV - Planned Value (Valor Planificado)

```dax
VAR _MaxContextDate = TODAY()
VAR _TotalDays = DATEDIFF(CALCULATE(MIN(Fact_Task[planned_startdate])),CALCULATE(MAX(Fact_Task[planned_enddate])),DAY)+1
VAR _RealEnd = IF([#Progress(%)]=1,CALCULATE(MAX(Fact_Task[real_enddate])),BLANK())
VAR _StatementDate = IF(ISBLANK(_RealEnd),_MaxContextDate,_RealEnd)
VAR _ElapsedDays = MAX(0,DATEDIFF(CALCULATE(MIN(Fact_Task[planned_startdate])),_StatementDate,DAY)+1)
VAR _ProgressPct = MIN(1,DIVIDE(_ElapsedDays,_TotalDays,0))
RETURN _ProgressPct*[#BU]
```

#VAC - Variance At Completion (Variación al Completar)

```dax
=[#BU]-[#EAC]
#CV(%) - Variación de Costo (%)
dax
=DIVIDE([#CV],[#EV],0)
```
</details>

#### 📅 Dates (Fechas)
<details>
<summary>Ver medidas de fechas</summary>


RealEndDate - Fecha de fin real

```dax
=IF([#Progress(%)] = 1,MAX(Fact_Task[real_enddate]),BLANK())
```

RealStartDate - Fecha de inicio real

```dax
=MIN(Fact_Task[real_startdate])
```

</details>

#### 🚦 KPIs (Indicadores Clave de Rendimiento)
<details>
<summary>Ver KPIs</summary>


#CPI - Cost Performance Index (Índice de Rendimiento de Costo)

```dax
VAR _CPI = 
    IF(
        ISBLANK([#AC]) || [#AC] = 0, 
        BLANK(), 
        DIVIDE([#EV], [#AC], BLANK())
    )
VAR _Result = IF(NOT(ISBLANK(_CPI)), MIN(_CPI, 3.00), BLANK())
RETURN _Result
```

#SPI - Schedule Performance Index (Índice de Rendimiento de Cronograma)

```dax
VAR _PlannedEnd = MAX(Fact_Task[planned_enddate])
VAR _Result = IF([Status] = "To Do" && [#EV] = 0 && _PlannedEnd>TODAY(), 
                BLANK(), 
                DIVIDE([#EV], [#PV],BLANK())
            )
RETURN _Result
```

</details>

#### 🎯 Status (Estado y Alertas)
<details>
<summary>Ver medidas de estado</summary>


Status - Estado de la tarea

```dax
VAR _Progress = [#Progress(%)]
VAR _RealStart = MIN(Fact_Task[real_startdate])
VAR _RealEnd = SWITCH(TRUE(),
                _Progress = 1,FORMAT(MAX(Fact_Task[real_enddate]),"DD/MM/YYYY"),
                _Progress < 1 = BLANK(),
                BLANK())
VAR _Result = SWITCH(TRUE(),
                ISBLANK(_RealStart) && ISBLANK(_RealEnd),"To Do",
                NOT(ISBLANK(_RealStart)) && ISBLANK(_RealEnd),"In Progress",
                "Done")
RETURN _Result
```

Status_Alert - Alerta de estado del proyecto

```dax
VAR _SPI = [#SPI]
VAR _CPI = [#CPI]
VAR _Result = SWITCH(TRUE(),
                ISBLANK(_SPI) && ISBLANK(_CPI), "Not Started",
                (NOT(ISBLANK(_CPI)) && _CPI<0.90) || (NOT(ISBLANK(_SPI)) && _SPI<0.90),"Critical",
                (NOT(ISBLANK(_CPI)) && _CPI<1) || (NOT(ISBLANK(_SPI)) && _SPI<1),"At Risk",
                (NOT(ISBLANK(_CPI)) && _CPI>=1) && (NOT(ISBLANK(_SPI)) && _SPI>=1),"Healthy")
RETURN _Result
```

</details>

#### 📈 YTD (Year-To-Date - Acumulado Anual)
<details>
<summary>Ver medidas YTD</summary>


#AC_YTD - Costo Real Acumulado Anual

```dax
VAR _DateMax = EOMONTH(TODAY(),0)
VAR _Result = IF(
    MAX(DimCalendar[Date]) <= _DateMax,
    TOTALYTD([#AC], DimCalendar[Date], DimCalendar[Date] <= _DateMax),
    BLANK()
)
RETURN _Result
```

#BU_YTD - Presupuesto Acumulado Anual

```dax
VAR _DateMax = EOMONTH(TODAY(),0)
VAR _Result = IF(
    MAX(DimCalendar[Date]) <= _DateMax,
    TOTALYTD([#BU], DimCalendar[Date], DimCalendar[Date] <= _DateMax),
    BLANK()
)
RETURN _Result
```

#EV_YTD - Valor Ganado Acumulado Anual

```dax
VAR _Todaydate = EOMONTH(TODAY(),0)
VAR _Result = IF(MAX(DimCalendar[Date]) <= _Todaydate,[#EV],BLANK())
RETURN _Result
```
#PV_YTD - Valor Planificado Acumulado Anual

```dax
VAR _Todaydate = EOMONTH(TODAY(),0)
VAR _Result = IF(MAX(DimCalendar[Date]) <= _Todaydate,[#PV],BLANK())
RETURN _Result
```

</details>

#### 🎨 Visuals (Visualizaciones HTML + DAX)
<details>
<summary>Ver CardOverview (Tarjetas KPI con diseño moderno)</summary>


Esta medida genera un dashboard HTML dinámico con tarjetas estilizadas usando gradientes CSS:

```dax
VAR _VirtualProjectsTable = 
  CALCULATETABLE(
      ADDCOLUMNS(
          SUMMARIZE(DimProjects, DimProjects[project_id]),
          "@Status", [Status],
          "@StatusAlert", [Status_Alert]
      ),
      ALLSELECTED()
  )
VAR _Budget_Format = 
    SWITCH(TRUE(),
        [#BU] >= 1000000, FORMAT([#BU] / 1000000, "$0.0") & "M",
        [#BU] >= 1000, FORMAT([#BU] / 1000, "$0.0") & "K",
        FORMAT([#BU], "$#,##0")
    )
VAR _AC_Format = 
    SWITCH(TRUE(),
        [#AC] >= 1000000, FORMAT([#AC] / 1000000, "$0.0") & "M",
        [#AC] >= 1000, FORMAT([#AC] / 1000, "$0.0") & "K",
        FORMAT([#AC], "$#,##0")
    )
[... Continuación con formatos para CV, EAC, VAC, CPI, SPI ...]
VAR _CountDone = COUNTROWS(FILTER(_VirtualProjectsTable, [@Status] = "Done"))
VAR _CountInProgress = COUNTROWS(FILTER(_VirtualProjectsTable, [@Status] = "In Progress"))
VAR _CountToDo = COUNTROWS(FILTER(_VirtualProjectsTable, [@Status] = "To Do"))
VAR _CountAtRisk = COUNTROWS(FILTER(_VirtualProjectsTable, [@StatusAlert] = "Critical" || [@StatusAlert] = "At Risk"))

RETURN
"
<html lang='es'>
<style>
:root{
    --bg:#f4f7fb;
    --card-radius:18px;
    --shadow:0 10px 25px rgba(0,0,0,.10);
    --text:#ffffff;
}
[... CSS completo con gradientes y estilos ...]
</style>
<body>
<div class='kpi-grid'>
    <!-- 12 tarjetas KPI con iconos y valores dinámicos -->
</div>
</body>
</html>
"
```

Esta medida incluye diseño responsivo con grid CSS, efectos hover, iconos emoji y formateo automático de números (K, M).
</details>

## 🖼️ Vistas Previas del Proyecto
<details>
<summary>📸 Ver Capturas de Pantalla</summary>


Página Overview
<img width="1924" height="1070" alt="Overview Dashboard" src="https://github.com/user-attachments/assets/3fb03df8-501b-4d0c-aae9-e0393e3b4d21" />

Página Project Details
<img width="1930" height="1068" alt="Project Details" src="https://github.com/user-attachments/assets/ee660bad-d843-4dd2-a1da-fdf3dcef3073" />

Página Analysis
<img width="1920" height="1071" alt="Analysis Dashboard" src="https://github.com/user-attachments/assets/e0e5f555-9c72-4518-84be-d68d4a6ad3cf" />
</details>

<details>
<summary>🎥 Ver Video Demostrativo</summary>

   
   Ver en YouTube: https://youtu.be/waNBncOlGs4
</details>

## 👤 Autor
Giancarlo Barrantes

Business Intelligence Developer | Power BI Specialist


<a href="https://www.linkedin.com/in/gb25"><img width="70" height="20" alt="image" src="https://github.com/user-attachments/assets/e0d69586-c6f9-4f66-9a3d-bc8dfa146463" /></a>
<a href="https://www.instagram.com/gbarrantes25"><img width="70" height="20" alt="image" src="https://github.com/user-attachments/assets/68773029-5e0f-49da-92e9-fb93a223dd09" />
</a>
<a href="https://www.tiktok.com/@gbarrantes25"><img width="65" height="20" alt="image" src="https://github.com/user-attachments/assets/349c87c0-1f3a-4368-8517-64a6012565c0" /></a>
<a href="https://www.youtube.com/@gbarrantes25"><img width="70" height="20" alt="image" src="https://github.com/user-attachments/assets/d9e2cb69-29ec-47fc-93ae-30896a7b533d" />
</a>

## 📝 Licencia
Este proyecto está bajo la Licencia MIT - ver el archivo <a href="https://mit-license.org/">LICENSE</a> para más detalles.

## ⭐ Agradecimientos
Si este proyecto te resultó útil, considera darle una ⭐ en GitHub y compartirlo con otros profesionales de Business Intelligence.

<div align="center">
   Hecho por <a href="https://gbarrantes25.github.io/">Giancarlo Barrantes</a>
</div>
