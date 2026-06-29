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
  - ```"postgresql://neondb_owner:YOURPASSWORD@ep-old-cloud-a8jxpwbf-pooler.eastus2.azure.neon.tech/neondb?sslmode=require&channel_binding=require"```, y se desglosará de la siguiente manera:
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
      VAR _CV_Format = 
          SWITCH(TRUE(),
              [#CV] >= 1000000, FORMAT([#CV] / 1000000, "$0.0") & "M",
              [#CV] >= 1000, FORMAT([#CV] / 1000, "$0.0") & "K",
              FORMAT([#CV], "$#,##0")
          )
      VAR _CPI_Format = 
          SWITCH(TRUE(),
              [#CPI] >= 1000000, FORMAT([#CPI] / 1000000, "0.00") & "M",
              [#CPI] >= 1000, FORMAT([#CPI] / 1000, "0.00") & "K",
              FORMAT(ROUND([#CPI],2),"#0.00","En-US")
          )
      VAR _SPI_Format = 
          SWITCH(TRUE(),
              [#SPI] >= 1000000, FORMAT([#SPI] / 1000000, "0.00") & "M",
              [#SPI] >= 1000, FORMAT([#SPI] / 1000, "0.00") & "K",
              FORMAT([#SPI], "#0.00","En-US")
          )
      VAR _EAC_Format = 
          SWITCH(TRUE(),
              [#EAC] >= 1000000, FORMAT([#EAC] / 1000000, "$0.0") & "M",
              [#EAC] >= 1000, FORMAT([#EAC] / 1000, "$0.0") & "K",
              FORMAT([#EAC], "$#,##0")
          )
      
      VAR _VAC_Format = 
          SWITCH(TRUE(),
              [#VAC] >= 1000000, FORMAT([#VAC] / 1000000, "$0.0") & "M",
              [#VAC] >= 1000, FORMAT([#VAC] / 1000, "$0.0") & "K",
              FORMAT([#VAC], "$#,##0")
          )
      
      VAR _CountDone = COUNTROWS(FILTER(
                                  _VirtualProjectsTable,
                                  [@Status] = "Done"
                              ))
      
      VAR _CountInProgress = COUNTROWS(FILTER(
                                  _VirtualProjectsTable,
                                  [@Status] = "In Progress"
                              ))
      
      VAR _CountToDo = COUNTROWS(FILTER(
                                  _VirtualProjectsTable,
                                  [@Status] = "To Do"
                              ))
      
      VAR _CountAtRisk = COUNTROWS(FILTER(
                                  _VirtualProjectsTable,
                                  [@StatusAlert] = "Critical" || [@StatusAlert] = "At Risk"
                              ))

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
      
      *{
          margin:0;
          padding:0;
          box-sizing:border-box;
          font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      }
      
      body{
          background:var(--bg);
          padding:30px;
      }
      
      .kpi-grid{
          display:grid;
          grid-template-columns:repeat(auto-fit,minmax(165px,1fr));
          gap:8px;
      }
      
      .kpi-card{
          position:relative;
          overflow:hidden;
          border-radius:var(--card-radius);
          padding:30px;
          color:var(--text);
          box-shadow:var(--shadow);
          transition:.3s ease;
      }
      
      .kpi-card:hover{
          transform:translateY(-4px);
      }
      
      .kpi-card::before{
          content:'';
          position:absolute;
          top:-40px;
          right:-40px;
          width:110px;
          height:110px;
          border-radius:50%;
          background:rgba(255,255,255,.12);
      }
      
      .kpi-header{
          display:flex;
          justify-content:space-between;
          align-items:center;
          margin-bottom:10px;
      }
      
      .kpi-title{
          font-size:.75rem;
          font-weight:700;
          opacity:.95;
      }
      
      .kpi-icon{
          width:40px;
          height:40px;
          border-radius:12px;
          background:rgba(255,255,255,.18);
          display:flex;
          align-items:center;
          justify-content:center;
          font-size:18px;
          backdrop-filter:blur(5px);
      }
      
      .kpi-value{
          font-size:1.2rem;
          font-weight:700;
          line-height:1;
      }
      
      .kpi-footer{
          margin-top:10px;
          font-size:.85rem;
          opacity:.9;
      }
      
      /* Gradientes individuales */
      .projects{
          background:linear-gradient(135deg,#4D70FF,#40ADF6);
      }
      
      .ToDo{
          background:linear-gradient(135deg,#757DFC,#CB7AFE);
      }
      
      .InProgress{
          background:linear-gradient(135deg,#E8697D,#EC9B65);
      }
      
      .Done{
          background:linear-gradient(135deg,#28a37d,#43D79D);
      }
      
      .AtRisk{
          background:linear-gradient(135deg,#d7327d,#E8697D);
      }
      
      
      .Budget, .Ac, .CV, .EAC, .VAC, .CPI, .SPI{
          background:linear-gradient(135deg,#4E6483,#99AAC2);
      }
      
      
      </style>
      <body>
      
      <div class='kpi-grid'>
      
          <!-- Total Proyectos -->
          <div class='kpi-card projects'>
              <div class='kpi-header'>
                  <div class='kpi-title'>Total Projects</div>
                  <div class='kpi-icon'>📁</div>
              </div>
              <div class='kpi-value'>"&[#ProjectsQuantity]&"</div>
          </div>
      
          <!-- Status To Do -->
          <div class='kpi-card ToDo'>
              <div class='kpi-header'>
                  <div class='kpi-title'>To-Do</div>
                  <div class='kpi-icon'>⏳</div>
              </div>
              <div class='kpi-value'>"&_CountToDo&"</div>
          </div>
      
          <!-- Status In Progress -->
          <div class='kpi-card InProgress'>
              <div class='kpi-header'>
                  <div class='kpi-title'>In Progress</div>
                  <div class='kpi-icon'>📌</div>
              </div>
              <div class='kpi-value'>"&_CountInProgress&"</div>
          </div>
      
          <!-- Status Done -->
          <div class='kpi-card Done'>
              <div class='kpi-header'>
                  <div class='kpi-title'>Done</div>
                  <div class='kpi-icon'>✅</div>
              </div>
              <div class='kpi-value'>"&_CountDone&"</div>
          </div>
      
          <!-- Status Alert At Risk -->
          <div class='kpi-card AtRisk'>
              <div class='kpi-header'>
                  <div class='kpi-title'>At Risk</div>
                  <div class='kpi-icon'>🚨</div>
              </div>
              <div class='kpi-value'>"&_CountAtRisk&"</div>
          </div>
      
          <!-- Budget -->
          <div class='kpi-card Budget'>
              <div class='kpi-header'>
                  <div class='kpi-title'>Total Budget</div>
                  <div class='kpi-icon'>💰</div>
              </div>
              <div class='kpi-value'>"&_Budget_Format&"</div>
          </div>
      
          <!-- AC -->
          <div class='kpi-card Ac'>
              <div class='kpi-header'>
                  <div class='kpi-title'>Total AC</div>
                  <div class='kpi-icon'>💵</div>
              </div>
              <div class='kpi-value'>"&_AC_Format&"</div>
          </div>
      
          <!-- CV -->
          <div class='kpi-card CV'>
              <div class='kpi-header'>
                  <div class='kpi-title'>CV</div>
                  <div class='kpi-icon'>🎛</div>
              </div>
              <div class='kpi-value'>"&_CV_Format&"</div>
          </div>
      
          <!-- EAC -->
          <div class='kpi-card EAC'>
              <div class='kpi-header'>
                  <div class='kpi-title'>EAC</div>
                  <div class='kpi-icon'>🎯</div>
              </div>
              <div class='kpi-value'>"&_EAC_Format&"</div>
          </div>
      
          <!-- VAC -->
          <div class='kpi-card VAC'>
              <div class='kpi-header'>
                  <div class='kpi-title'>VAC</div>
                  <div class='kpi-icon'>📊</div>
              </div>
              <div class='kpi-value'>"&_VAC_Format&"</div>
          </div>
      
          <!-- CPI -->
          <div class='kpi-card CPI'>
              <div class='kpi-header'>
                  <div class='kpi-title'>CPI</div>
                  <div class='kpi-icon'>🚦</div>
              </div>
              <div class='kpi-value'>"&_CPI_Format&"</div>
          </div>
      
          <!-- SPI -->
          <div class='kpi-card SPI'>
              <div class='kpi-header'>
                  <div class='kpi-title'>SPI</div>
                  <div class='kpi-icon'>📆</div>
              </div>
              <div class='kpi-value'>"&_SPI_Format&"</div>
          </div>
      
      </div>
      </body>
      </html>
      "
      ```
  </details>
  
  - <details><summary>YTD:</summary>

    - #AC_YTD:
      ``` 
    	VAR _DateMax = EOMONTH(
    		TODAY(),
    		0
    	)
    	VAR _Result = IF(
    		MAX(DimCalendar[Date]) <= _DateMax,
    		TOTALYTD(
    			[#AC],
    			DimCalendar[Date],
    			DimCalendar[Date] <= _DateMax
    		),
    		BLANK()
    	)
    	RETURN
    		_Result
      ```
    - #BU_YTD:
      ``` 
    	VAR _DateMax = EOMONTH(
    		TODAY(),
    		0
    	)
    	VAR _Result =
    	IF(
    		MAX(DimCalendar[Date]) <= _DateMax,
    		TOTALYTD(
    			[#BU],
    			DimCalendar[Date],
    			DimCalendar[Date] <= _DateMax
    		),
    		BLANK()
    	)
    
    	RETURN
    		_Result
      ```
    - #EV_YTD:
      ``` 
      VAR _Todaydate = EOMONTH(TODAY(),0)
      VAR _Result = IF(MAX(DimCalendar[Date]) <= _Todaydate,[#EV],BLANK())
      RETURN _Result
      ```
    - #PV_YTD:
      ``` 
      VAR _Todaydate = EOMONTH(TODAY(),0)
      VAR _Result = IF(MAX(DimCalendar[Date]) <= _Todaydate,[#PV],BLANK())
      RETURN _Result
      ```
  </details>
- Diseño Interactivo: Uso de paginado para navegación y segmentación de datos.

## 🖼️ Vistas Previas del proyecto
<details>
  <summary>Capturas</summary>
    <img width="1924" height="1070" alt="image" src="https://github.com/user-attachments/assets/3fb03df8-501b-4d0c-aae9-e0393e3b4d21" />
    <img width="1930" height="1068" alt="image" src="https://github.com/user-attachments/assets/ee660bad-d843-4dd2-a1da-fdf3dcef3073" />
    <img width="1920" height="1071" alt="image" src="https://github.com/user-attachments/assets/e0e5f555-9c72-4518-84be-d68d4a6ad3cf" />
</details>

<details>
  <summary>Video</summary>
  https://www.youtube.com/watch?v=ISpue331_Y8
</details>



## 👤 Autor
- Giancarlo Barrantes
- Lima, Perú
- [Linkedin](https://www.linkedin.com/in/gb25/)
