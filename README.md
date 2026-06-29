# Project Management Dashboard

## 📃 Descripción General
Dashboard ejecutivo para Directores Financieros (CFO) desarrollado en Power BI, diseñado para centralizar y analizar el estado financiero de una cadena de restaurantes en tiempo real.
Este proyecto transforma datos contables en información estratégica mediante:

- 📊 3 Vistas Analíticas: Overview, P&C Deep Dive y P&L (Estado de Resultados).
- 💰 Estado de Resultados: Ingresos, COGS, gastos operativos, EBIT, EBITDA y utilidad neta.
- 📈 KPIs Financieros: +30 medidas DAX (márgenes, costos, punto de equilibrio).
- 🎯 Metas vs Real: Comparativa de márgenes objetivo vs. alcanzados.
- 📅 Análisis Temporal: Tendencias mensuales y comparativas YoY (Year over Year).


## 📊 Contenido del proyecto
- Página "Overview": Contiene una vista general del análisis.
- Página "P&C Deep Dive": Contiene el análisis por sucursal de la cadena.
- Página "P&L": Contiene el esdtado de resultados de la empresa.


## 🛠️ Herramientas y Tecnologías Utilizadas
- Visualización: Power BI Desktop.
- Fuente de Datos:
  - [DimCenterCost.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vQ6PFClLjBQaGVinUTTAXvkXU9rfNwU-3kiuux2qMyL-WZl7p7AjV3gJRHp8UhZhuz_MLB8PLyaL8z1/pubhtml?gid=1517220622&single=true)
  - [DimDocument.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vTQruzVx53OPt6nqljWb9V0UhzNSA-1QDAqtTc7W8rvEqivT19gLfvHCmXC9q5nKpwBjT9Qh_1o7595/pubhtml?gid=775268392&single=true)
  - [DimEntity.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vQdYxEJXUrJNWj8e_BcxMak93N0ovnqHPsCZdokK_iZm4UyWCMBerzpfZDUcbVO4ma1ZKAYeFwKGh-V/pubhtml?gid=1860496830&single=true)
  - [DimProduct.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vSTxIpD47q6jTtZ_hJNbzMUgSWeUIa_PyrKuRk5f5HUcqzEKc27Lz0iDX_Jb09mLrxgNussrsBu7dT5/pubhtml?gid=248530384&single=true)
  - [DimSubcategory.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vR-7RPQosGMlYZkvK3IKwo5y0rfu0R30MR5KTvHY-vq-UF476O0ussKhzuJ5_hfCZowZ9Zo9WmoyX_C/pubhtml?gid=571723735&single=true)
  - [DimBranch.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vRrnh7vDS9fWZCEFM33f8N4ONGDAaNxtVZlCXSu1Xkk4VzeMw6crpXo3oNwvHwDch6I3bS6qCbjt-tq/pubhtml?gid=1431746542&single=true)
  - [Fact-Expenses.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vReNKN_bEEzeArYYgZhg6byO1LisSmkZIlHusdULdL_1yk5RSIeJKxbUInfsExxUYYQ9YbpSXchhv7X/pubhtml?gid=354373430&single=true)
  - [Fact-Revenue.csv](https://docs.google.com/spreadsheets/d/e/2PACX-1vRebdrai3eny64GPrLjRAXhuSBqzXV18BFVRNUi4ULliH3oE4Py7HgJ5Ri4DK8QM9S6jmMKjRUqZrcG/pubhtml?gid=147943285&single=true)

    
- Lenguajes: DAX para las medidas calculadas y Power Query (Lenguaje M) para la transformación de datos.


## ⚙️ Configuración del Entorno
- Software Necesario: Power BI Desktop.
- Instalación:
  - Descargar [Chief Financial Officer.pbix](https://github.com/Gbarrantes25/CFO-Dashboard-PowerBI/blob/main/Chief%20Financial%20Officer.pbix) con Power BI Desktop.
  - Entrar a Inicio y darle click a "Actualizar".


## 📂 Estructura del Repositorio
<code>.
  ├── Data Source                        # Contiene las tablas en formato .xlsx
  |── Images                             # Carpeta donde se alojan los íconos.
  ├── Chief Financial Officer.pbix       # Archivo que será ejecutado con Power BI Desktop.
  |── UDF.txt                            # Contiene las funciones definidas por el usuario.                
  └── README.md                          # Este archivo.
</code>


## ✅ Características Principales
- Transformaciones en Power Query: Se realizaron procesos de limpieza y modelado de datos para optimizar el rendimiento.
- Creación de tabla calendario.
- Medidas DAX:
  - <details><summary>Abrir</summary>
    <code>
      MEASURE '_Measures'[#Revenue] = SUMX(
    			Fact_Revenue,
    			Fact_Revenue[Quantity] * RELATED(DimProducts[Price_Unit])
    		)
    	MEASURE '_Measures'[#Expenses] = SUMX(
    			Fact_Expenses,
    			Fact_Expenses[Subtotal]
    		)
    	MEASURE '_Measures'[#Net Income] = [#EBT] - [#Income Tax Expense]
    	MEASURE '_Measures'[#Net Margin on Op.Rev(%)] = FX_OVEROPERATINGREVENUE([#Net Income])
    	MEASURE '_Measures'[#BEP Sales] = DIVIDE(
    			[#Fixed Cost],
    			1 - FX_OVEROPERATINGREVENUE([#Variable Cost]),
    			0
    		)
    	MEASURE '_Measures'[#COGS Ratio(%)] = FX_OVEROPERATINGREVENUE(FX_EXPENSECATEGORY({
    			"Cost of Goods Sold"
    		}))
    	MEASURE '_Measures'[#Gross Profit] = FX_REVENUECATEGORY({
    			"Revenue"
    		}) - FX_EXPENSECATEGORY({
    			"Cost of Goods Sold"
    		})
    	MEASURE '_Measures'[#Opex] = FX_EXPENSELINEITEM({
    			"Operating Expenses"
    		})
    	MEASURE '_Measures'[#EBIT] = [#Gross Profit] - [#Opex]
    	MEASURE '_Measures'[#Income Tax Expense] = [#EBT] * 0.295
    	MEASURE '_Measures'[#EBT] = [#EBIT] + FX_REVENUECATEGORY({
    			"Other Income"
    		}) - FX_EXPENSECATEGORY({
    			"Financial Expenses"
    		})
    	MEASURE '_Measures'[#Depreciation] = FX_EXPENSECATEGORY({
    			"Depreciation Expenses"
    		})
    	MEASURE '_Measures'[#EBITDA] = [#EBIT] + [#Depreciation]
    	MEASURE '_Measures'[#Variable Cost] = FX_EXPENSECOSTTYPE({
    			"Variable Cost"
    		})
    	MEASURE '_Measures'[#Fixed Cost] = FX_EXPENSECOSTTYPE({
    			"Fixed Cost"
    		})
    	MEASURE '_Measures'[#Gross Profit Margin(%)] = FX_OVEROPERATINGREVENUE([#Gross Profit])
    	MEASURE '_Measures'[#Operating Margin(%)] = FX_OVEROPERATINGREVENUE([#EBIT])
    	MEASURE '_Measures'[#EBITDA Margin(%)] = FX_OVEROPERATINGREVENUE([#EBIT] + [#Depreciation])
    	MEASURE '_Measures'[#Operating Revenue] = FX_REVENUECATEGORY({
    			"Revenue"
    		})
    	MEASURE '_Measures'[#Cost of Goods Sold] = FX_EXPENSESUBCATEGORY({
    			"Supplies and Materials",
    			"Packaging",
    			"Beverages"
    		})
    	MEASURE '_Measures'[#Store Operating Expenses] = FX_EXPENSECATEGORY({
    			"Store Operating Expenses"
    		})
    	MEASURE '_Measures'[#Selling Expenses] = FX_EXPENSECATEGORY({
    			"Selling Expenses"
    		})
    	MEASURE '_Measures'[#Administrative Expenses] = FX_EXPENSECATEGORY({
    			"Administrative Expenses"
    		})
    	MEASURE '_Measures'[#Other Income] = FX_REVENUECATEGORY({
    			"Other Income"
    		})
    	MEASURE '_Measures'[#Financial Expenses] = FX_EXPENSECATEGORY({
    			"Financial Expenses"
    		})
    	MEASURE '_Measures'[#Net Margin on Total Rev(%)] = DIVIDE(
    			[#Net Income],
    			[#Revenue],
    			0
    		)
    	MEASURE '_Measures'[#Margin of Safety(%)] = DIVIDE(
    			[#Operating Revenue] - [#BEP Sales],
    			[#Operating Revenue],
    			0
    		)
    	MEASURE '_Measures'[#LY Operating Revenue] = FX_LY([#Operating Revenue])
    	MEASURE '_Measures'[#YoY Operating Rev(%)] = FX_YOY(
    			[#Operating Revenue],
    			[#LY Operating Revenue]
    		)
    	MEASURE '_Measures'[SelectedImg] = VAR result =
    		SWITCH(
    			TRUE(),
    			SELECTEDVALUE(
    				DimBranch[Branch],
    				"Seleccionar todo"
    			) = "Seleccionar todo", FX_CHOOSEIMG(1),
    			SELECTEDVALUE(DimBranch[Branch]) = "Oficina Central", FX_CHOOSEIMG(2),
    			SELECTEDVALUE(DimBranch[Branch]) = "T. Dasso", FX_CHOOSEIMG(3),
    			SELECTEDVALUE(DimBranch[Branch]) = "T. Arequipa", FX_CHOOSEIMG(4),
    			SELECTEDVALUE(DimBranch[Branch]) = "T. Ica", FX_CHOOSEIMG(5),
    			SELECTEDVALUE(DimBranch[Branch]) = "T. Chiclayo", FX_CHOOSEIMG(6),
    			SELECTEDVALUE(DimBranch[Branch]) = "T. Cusco", FX_CHOOSEIMG(7)
    		)
    		RETURN
    			result
    	MEASURE '_Measures'[CardBranch] = FX_CHOOSECARD(2)
    	MEASURE '_Measures'[CardOverview] = FX_CHOOSECARD(1)
    	MEASURE '_Measures'[COGS(%) Target] = 0.3
    	MEASURE '_Measures'[Operating Margin (%) Target] = 0.12
    	MEASURE '_Measures'[CardPL] = FX_CHOOSECARD(3)
    	MEASURE '_Measures'[#ExpOverOpeRev(%)] = DIVIDE(
    			[#Expenses],
    			CALCULATE(
    				[#Operating Revenue],
    				ALL('Calendar'[#Month]),
    				ALL(DimSubcategory[Subcategory])
    			),
    			0
    		)
    </code>
    </details> 
- Funciones Definidas por el Usuario (UDF): [UDF](https://github.com/Gbarrantes25/CFO-Dashboard-PowerBI/blob/main/Udf.txt)
- Diseño Interactivo: Uso de paginado para navegación, marcadores y segmentación de datos.

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
