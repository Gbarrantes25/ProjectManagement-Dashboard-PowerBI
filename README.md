# Project Management Dashboard

## 📃 Descripción General
Dashboard porfesional para Project Management desarrollado en Power BI, diseñado para centralizar y analizar los proyectos y tareas de una empresa.
Este proyecto se conecta a una base de datos remota NeonTech (PostgreSQL) transforma datos en información accionable mediante:

- 📊 5 Vistas Analíticas: Overview, Project Details, Analysis, Task Status y Schelude & Timeline.
- 📈 KPIs para proyectos: +20 medidas DAX (CPI, SPI, CV, Budget, AC, VAC, etc).
- 🎯 AC vs Budget: Comparativa de costos reales vs presupuesto (por proyecto y tarea).
- 📅 Análisis Temporal: Tendencias mensuales, acumulados y diagrama de Gantt.


## 📊 Contenido del proyecto
- Página "Overview": Contiene una vista general del análisis.
- Página "Project Details": Contiene el detalle por proyecto (cuánto se va gastanto, el CPI vs SPI y Gantt).
- Página "Analysis": Contiene el análisis por proyecto y fase.
- Página "Task Status": Contiene el detalle por tarea y responsable.
- Página "Schelude & Timeline": Contiene un análisis por tarea entre lo planificado vs real.


## 🛠️ Herramientas y Tecnologías Utilizadas
- Software Necesario: Power BI Desktop, PostgreSQL o DBeaver y NeonTech.
- Fuente de Datos:
  - [projects.csv](https://github.com/Gbarrantes25/ProjectManagement-Dashboard-PowerBI/blob/main/Data%20Source/projects.csv)
- Custom Visual para Gantt: [Power Gantt Chart](https://marketplace.microsoft.com/en-us/product/power-bi-visuals/novasilvagroupbv1626174611009.powerganttchart?tab=overview)
- Lenguajes: SQL para extracción de tablas, DAX para las medidas calculadas y Power Query (Lenguaje M) para la transformación de datos.


## ⚙️ Configuración del Entorno
- Software Necesario: Power BI Desktop.
- Instalación:
  - Descargar Power BI Desktop.
  - Instalar [PostgreSQL](https://www.postgresql.org/download/) o [DBeaver](https://dbeaver.io/download/) en tu PC.
- Entrar a NeonTech (https://neon.com/)
- Neontech Credenciales:
  - Nos dirigimos al apartado "Connect to your project"/"Connection String", esto logrará que tengas credenciales similares a esta:
  - "postgresql://neondb_owner:YOURPASSWORD@ep-old-cloud-a8jxpwbf-pooler.eastus2.azure.neon.tech/neondb?sslmode=require&channel_binding=require", y se desglosará de la siguiente manera:
    - Database: neondb
    - Server: ep-old-cloud-a8jxpwbf-pooler.eastus2.azure.neon.tech
    - Port: 5432
    - Usuario: neondb_owner
    - Contraseña: (La misma que tiene tu proyecto).
- Vamos a DBeaver o PgAdmin4 y agregamos una conexión donde colocaremos las credenciales citadas anteriormente.
- Ya con la conexión establecidad, nos dirigimos al esquema y en la base de datos crearemos nuestra tabla (usaré script SQL):
  - <details><Summary>Crear Tabla</Summary>
    
    ```
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
  	CONSTRAINT projects_pkey PRIMARY KEY (id));
    ```
  </details> 
- Para cerrar la fase de DBBeaver o PgAdmin4, importamos el archivo project.csv y listo.
- Vamos a Power BI en el apartado de Power Query a conectarnos a una Base de datos PostgreSQL. Cuando solicite las credenciales, le brindamos las que obtuvimos en puntos anteriores. Este paso lo repetiremos 6 veces, ya que necesitaremos extraer 6 tablas, lo único que se modificará es la instrucción SQL de la conexión:
  - DateSource: Es la tabla unión de fechas planificadas y reales del proyecto.
    - <details><Summary>SQL</Summary>
      
      ```
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
  - DimProjects: Tabla dimensión de proyectos.
    - <details><summary>SQL</summary>
      
      ```
      SELECT DISTINCT project_id,project_name,area,project_type FROM projects ORDER BY project_id
      ```
    </details>
  - DimProjectManagement: Tabla dimensión de jefes de proyecto.
    - <details><summary>SQL</summary>
 
      ```
      SELECT DISTINCT project_management_id,project_management FROM projects ORDER BY project_management_id
      ```
    </details>
  - DimPhase: Tabla dimensión de fases de proyecto.
    - <details><summary>SQL</summary>
      
      ```
      SELECT DISTINCT phase_id,phase FROM projects ORDER BY phase_id
      ```
    </details>
  - DimTask: Tabla dimensión de tareas.
    - <details><summary>SQL</summary>
    
      ```
      SELECT DISTINCT task_id,task_name,project_id,planned_startdate,planned_enddate from projects order BY task_id ASC
      ```
    </details>
  - Fact_Task: Tabla de hechos histórica de tareas.
    - <details><summary>SQL</summary>

      ```
      SELECT 
          Id,
          task_id,
          task_name,
          project_id,
          project_management_id
          ,phase_id,
          phase_weight,
          "owner" ,
          planned_startdate,
          planned_enddate,
          real_startdate,
          real_enddate,
          budget,
          AC,
          Progress_Acc,
          Data_Date
      from projects
      ``` 
    </details>



## 📂 Estructura del Repositorio
<code>.
  ├── Data Source                        # Contiene la tabla en formato .csv
  ├── Project Management Dashboard.pbix  # Archivo que será ejecutado con Power BI Desktop.
  └── README.md                          # Este archivo.
</code>


## ✅ Características Principales
- Transformaciones en Power Query: Se realizaron procesos de limpieza y modelado de datos para optimizar el rendimiento.
- Creación de tabla calendario.
- Medidas DAX:
  - <details><summary>Basics:</summary>
    
      - #CountTask:
        ```
        =DISTINCTCOUNT(Fact_Task[task_id])
        ```
      - #ProjectsQuantity:
        ```
        =DISTINCTCOUNT(Fact_Task[project_id])
        ```
      - #CountDone:
        ```
        =COUNTROWS(FILTER(
            ADDCOLUMNS(
                SUMMARIZE(
                    Fact_Task,
                    DimTask[task_id]
                ),
                "Status1", [Status]
            ),[Status1]="Done")
        )
        ```
      - #CountInProgress:
        ```
        =COUNTROWS(FILTER(
            ADDCOLUMNS(
                SUMMARIZE(
                    Fact_Task,
                    DimTask[task_id]
                ),
                "Status1", [Status]
            ),[Status1]="In Progress")
        )
        ```
      - #CountToDo:
        ```
        = COUNTROWS(FILTER(
            ADDCOLUMNS(
                SUMMARIZE(
                    Fact_Task,
                    DimTask[task_id]
                ),
                "Status1", [Status]
            ),[Status1]="To Do")
        )
        ```
  </details>
  
  - <details><summary>Calculations:</summary>
    
      - #AC:
        ```
        =SUM(Fact_Task[ac])
        ```
      - #BU:
        ```
        VAR _Result = SUMX(VALUES(Fact_Task[task_id]),CALCULATE(MAX(Fact_Task[budget])))
        RETURN _Result
        ```
      - #CV:
        ```
        =[#EV]-[#AC]
        ```
      - #EAC:
        ```
        = IF(
            ISBLANK([#CPI]) || [#CPI] = 0,
            [#BU],
            DIVIDE([#BU], [#CPI])
          )
        ```
      - #EV:
        ```
        =[#BU]*[#Progress(%)]
        ```
      - #Executed_Budget:
        ```
        =DIVIDE([#AC],[#BU],0)
        ```
      - #Progress(%):
        ```
        VAR _RealProgress = SUMX(VALUES(Fact_Task[task_id]),CALCULATE(MAX(Fact_Task[phase_weight])*CALCULATE(MAX(Fact_Task[progress_acc]))))
        VAR _TotalProgress = SUMX(VALUES(Fact_Task[task_id]),CALCULATE(MAX(Fact_Task[phase_weight])))
        RETURN DIVIDE(_RealProgress,_TotalProgress,0)
        ```
      - #PV:
        ``` 
        VAR _MaxContextDate = TODAY()
        VAR _TotalDays = DATEDIFF(CALCULATE(MIN(Fact_Task[planned_startdate])),CALCULATE(MAX(Fact_Task[planned_enddate])),DAY)+1
        VAR _RealEnd = IF([#Progress(%)]=1,CALCULATE(MAX(Fact_Task[real_enddate])),BLANK())
        VAR _StatementDate = IF(ISBLANK(_RealEnd),_MaxContextDate,_RealEnd)
        VAR _ElapsedDays = MAX(0,DATEDIFF(CALCULATE(MIN(Fact_Task[planned_startdate])),_StatementDate,DAY)+1)
        VAR _ProgressPct = MIN(1,DIVIDE(_ElapsedDays,_TotalDays,0))
        RETURN _ProgressPct*[#BU]
        ```
      - #VAC:
        ```
        =[#BU]-[#EAC]
        ```
      - #CounTaskPlanned:
        ``` 
        VAR _datef = EOMONTH(TODAY(),0)
      	VAR _maxdate = EOMONTH(
      		MAX(DimCalendar[Date]),
      		0
      	)
      	VAR _mindate = EOMONTH(
      		MAX(DimCalendar[Date]),
      		-1
      	) + 1
    	  VAR _Count =COUNTROWS(
            	SUMMARIZECOLUMNS(
            		DimTask[task_id],
            		DimTask[planned_enddate],
            		DimTask[Real_EndDate],
            		FILTER(
            			ALL(DimTask[planned_enddate]),
            			DimTask[planned_enddate] >= _mindate && DimTask[planned_enddate] <= _maxdate
            		),
            		"Progress", [#Progress(%)]
            	))
    	  RETURN
    		  IF(_maxdate <= _datef,_Count,BLANK())
        ```
      - #CounTaskReal:
        ```
        VAR _datef = EOMONTH(TODAY(),0)
      	VAR _maxdate = EOMONTH(
      		MAX(DimCalendar[Date]),
      		0
      	)
      	VAR _mindate = EOMONTH(
      		MAX(DimCalendar[Date]),
      		-1
      	) + 1
      	VAR _Count =COUNTROWS(
      	SUMMARIZECOLUMNS(
      		DimTask[task_id],
      		DimTask[planned_enddate],
      		DimTask[Real_EndDate],
      		FILTER(
      			ALL(DimTask[Real_EndDate]),
      			DimTask[Real_EndDate] >= _mindate && DimTask[Real_EndDate] <= _maxdate
      		),
      		"Progress", [#Progress(%)]
      	))
      	RETURN
      		IF(_maxdate <= _datef,_Count,BLANK())
        ```
      - #CV(%):
        ```
        =DIVIDE([#CV],[#EV],0)
        ```
  </details>
  
    - <details><summary>Dates:</summary>
    
      - RealEndDate:
        ```
        =IF([#Progress(%)] = 1,MAX(Fact_Task[real_enddate]),BLANK())
        ```
      - RealStartDate:
        ```
        =MIN(Fact_Task[real_startdate])
        ```
  </details>
  
    - <details><summary>KPI:</summary>
      
      - #CPI:

        ```
        VAR _CPI = 
            IF(
                ISBLANK([#AC]) || [#AC] = 0, 
                BLANK(), 
                DIVIDE([#EV], [#AC], BLANK())
            )
        VAR _Result = IF(NOT(ISBLANK(_CPI)), MIN(_CPI, 3.00), BLANK())
        RETURN _Result
        ```
      - #SPI:
        ```
        VAR _PlannedEnd = MAX(Fact_Task[planned_enddate])
        VAR _Result = IF([Status] = "To Do" && [#EV] = 0 && _PlannedEnd>TODAY(), 
                        BLANK(), 
                        DIVIDE([#EV], [#PV],BLANK())
                    )
        RETURN _Result
        ```
  </details>

  - <details><summary>Status:</summary>
    
    - Status:
      ``` 
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
    - Status_Alert:
      ``` 
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

  - <details><summary>Visuals:</summary>
    
    - CardOverview:
      ```
      
      ```
  </details>
- Diseño Interactivo: Uso de paginado para navegación y segmentación de datos.

## 🖼️ Vistas Previas del proyecto
<details>
  <summary>Capturas</summary>
    <img width="1911" height="1064" alt="image" src="https://github.com/user-attachments/assets/a5976e58-5afd-4d45-ac86-79416ee37b7d" />
    <img width="1910" height="1067" alt="image" src="https://github.com/user-attachments/assets/65107f03-de5f-4e78-89cf-f04146ff8ab0" />
    <img width="1905" height="1060" alt="image" src="https://github.com/user-attachments/assets/7ac6b2d9-ecb9-4628-add3-75c9ee545ac6" />

</details>

<details>
  <summary>Video</summary>
  https://www.youtube.com/watch?v=ISpue331_Y8
</details>



## 👤 Autor
- Giancarlo Barrantes
- Lima, Perú
- [Linkedin](https://www.linkedin.com/in/gb25/)
