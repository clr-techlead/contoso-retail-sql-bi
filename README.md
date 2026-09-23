# Contoso Retail SQL & BI

Modelo de datos SQL y dashboard en Power BI para Contoso Retail, un minorista sintetico de electronica multi-region (LatAm). Proyecto de portafolio (no un sistema en produccion), en la misma linea que [SLA Command Center](https://github.com/clr-techlead/sla-command-center).

## Objetivo

Simular las ventas de una cadena de retail y responder preguntas de negocio tipicas de un analista de BI:

- Como se comportan las ventas por region, tienda y categoria de producto?
- - Que productos y categorias generan mayor margen de utilidad?
  - - Como varia el ticket promedio y el volumen de ventas por segmento de cliente?
   
    - ## Arquitectura de datos
   
    - Modelo semantico en esquema estrella con 5 tablas: 1 de hechos y 4 dimensiones.
   
    - | Tabla | Tipo | Contenido |
    - |---|---|---|
    - | Fact_Sales.csv | Hecho | ~12,200 transacciones de venta: fecha, producto, tienda, cliente, cantidad, precio unitario, descuento, costo y utilidad |
    - | Dim_Date.csv | Dimension | Calendario 2024-01-01 a 2026-08-31 (ano, trimestre, mes, dia de la semana) |
    - | Dim_Product.csv | Dimension | Catalogo de productos: nombre, categoria, subcategoria, marca, costo y precio unitario |
    - | Dim_Store.csv | Dimension | 11 tiendas en Colombia, Peru, Ecuador, Chile, Argentina y Mexico, con region y tipo de tienda |
    - | Dim_Customer.csv | Dimension | Segmento de cliente (Consumer/Enterprise) y grupo de edad |
   
    - ### Diagrama entidad-relacion
   
    - ```
      Dim_Date -------\
      Dim_Product ------\
      Dim_Store ----------> Fact_Sales
      Dim_Customer -----/
      ```

      Fact_Sales se relaciona 1 a N con cada dimension por clave subrogada (ProductKey, StoreKey, CustomerKey, Date). Todas las relaciones son de direccion unica, sin relaciones circulares.

      ## Medidas DAX clave

      ```dax
      Total Ventas = SUM(Fact_Sales[SalesAmount])

      Utilidad Total = SUM(Fact_Sales[ProfitAmount])

      Margen Pct = DIVIDE([Utilidad Total], [Total Ventas])

      Ticket Promedio = DIVIDE([Total Ventas], DISTINCTCOUNT(Fact_Sales[OrderID]))
      ```

      Sobre esa misma base se calculan medidas de tendencia interanual y participacion de ventas por region y categoria.

      ## Roadmap

      Incorporar el modelo a un pipeline de refresco incremental en Azure SQL y ampliar el dashboard con un analisis de cohortes de clientes.

      ## Stack

      SQL, Power BI (DAX, Power Query), modelado dimensional.

      ---

      Camilo Andres Leon Rubriche - linkedin.com/in/caleru
      
