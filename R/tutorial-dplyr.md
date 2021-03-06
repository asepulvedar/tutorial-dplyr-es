Tutorial dplyr
==============

### Introducción

#### ¿Que es **dplyr**?

**[dplyr](https://github.com/hadley/dplyr)** es una librería de funciones para analizar y manipular datos: dividir grandes colecciones de datos, aplicar una función a cada parte y re-agrupar las, y también aplicar filtros, ordenar y juntar datos. Es una evolución del paquete **[plyr](http://plyr.had.co.nz/)**: es más rápido, capaz de trabajar sobre datos remotos y solo trabaja sobre data.frames.

Como lo presenta su autor, Hadley Wickham, **[dplyr](https://github.com/hadley/dplyr)** es la *nueva* iteración del paquete **plyr**, enfocado a las **data.frames**, con 3 objetivos:

-   identificar cual son las manipulaciones más importantes para analizar datos y hacerlas fáciles con R.

-   escribir las partes-llaves en [C++](http://www.rcpp.org/) para manipular los datos en memoria muy rápidamente.

-   usar las misma interfaces para trabajar donde sea los datos: data frame, data table o database.

#### objetivo del tutorial

-   entender los conceptos básicos de **dplyr**
-   aprender su *gramática*
-   saber con que objetos puede trabajar

Trabajaremos sobre los siguientes datos:

-   los movimientos de las tristemente famosas *tarjetas black* de Caja Madrid

![rato](images/aprende.jpg)

**Requerimientos**: Es necesario un conocimiento básico de R y saber como instalar paquetes.

> La integralidad de este tutorial esta en un repositorio público de Github: <http://github.com/fdelaunay/tutorial-dplyr-es> ¡Cualquier colaboración/correción esta bienvenida!

#### Documentación

Documentación del paquete (una vez instalado):

``` r
??dplyr
```

Tutoriales en inglés:

-   Vignette: [*Inroduction to dplyr*](http://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html)
-   Video: [*dplyr presentado por Hadley*](http://datascience.la/hadley-wickhams-dplyr-tutorial-at-user-2014-part-1/)

#### Instalación y cargamento

Este tutorial fue escrito con la versión `0.4.1` de **dplyr**.

    ## Warning: package 'dplyr' was built under R version 3.1.3

#### Los datos

El paquete "**tarjetasblack**" contiene dos objetos:

1.  La tabla `movimientos`, una data.frame que lista todos los movimientos realizados, el importe, la fecha y hora, el nombre del comercio y una clasificación del tipo de actividad.
2.  La table `miembros`, otra data.frame que lista los proprietarios de tarjeta, su función (consejal o directivo) así que la organisación de origen (partido politico o sindicato).

``` r
devtools::install_github("splatsh/tarjetasblack")
library(tarjetasblack)
```

``` r
str(movimientos)
```

    ## 'data.frame':    77202 obs. of  8 variables:
    ##  $ nombre            : Factor w/ 83 levels "Alberto Recarte GarcÃ­a Andrade",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ fecha             : POSIXct, format: "2003-01-04" "2003-01-04" ...
    ##  $ hora              : int  12 12 19 15 16 15 10 12 15 15 ...
    ##  $ minuto            : int  30 32 7 31 5 27 20 58 25 28 ...
    ##  $ importe           : num  38.7 14.6 95.6 49.1 13.9 ...
    ##  $ comercio          : chr  "RCG OFICINA                   " "MANZANIL AREA                 " "REST REAL C GOLF SOTOGRAN     " "ESTACONES DE SERVICIO ML      " ...
    ##  $ actividad_completa: chr  "CONFECCION TEXTIL EN GENERAL" "HOTELES,MOTELES,BALNEARIOS,CAMPINGS REST" "RESTAURANTES RESTO" "GASOLINERAS" ...
    ##  $ actividad         : Factor w/ 37 levels "","AGRICULTURA",..: 28 21 27 11 11 21 11 12 27 11 ...

``` r
str(miembros)
```

    ## 'data.frame':    83 obs. of  3 variables:
    ##  $ funcion     : Factor w/ 2 levels "consejal","directivo": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ nombre      : Factor w/ 83 levels "Alberto Recarte GarcÃ­a Andrade",..: 1 2 3 4 5 6 7 8 9 10 ...
    ##  $ organizacion: Factor w/ 11 levels "","CC OO","CEIM",..: 8 3 10 7 8 2 10 3 8 8 ...

### Fuentes de datos

#### Clase `tbl`

**dplyr** trabaja con objeto de la clase `tbl` (dato con estructura tabular). Es capaz de convertir automaticamente varios tipos de fuente de datos, que sean locales o lejanas (bases de datos).

#### Data frames

``` r
# data frame
head(miembros)
##    funcion                                   nombre    organizacion
## 1 consejal          Alberto Recarte GarcÃ­a Andrade Partido Popular
## 2 consejal                 Alejandro Couceiro Ojeda            CEIM
## 3 consejal Ãngel Eugenio GÃ³mez del Pulgar Perales            PSOE
## 4 consejal                 Angel Rizaldos GonzÃ¡lez Izquierda Unida
## 5 consejal                  Antonio CÃ¡mara Eguinoa Partido Popular
## 6 consejal  Antonio Rey de ViÃ±as SÃ¡nchez-Majestad           CC OO

# convertimos a la clase "tbl"
miembros <- tbl_df(miembros)

# los objetos "tbl" son mas facil de visualisar en la consola:
miembros
## Source: local data frame [83 x 3]
## 
##     funcion                                   nombre    organizacion
## 1  consejal          Alberto Recarte GarcÃ­a Andrade Partido Popular
## 2  consejal                 Alejandro Couceiro Ojeda            CEIM
## 3  consejal Ãngel Eugenio GÃ³mez del Pulgar Perales            PSOE
## 4  consejal                 Angel Rizaldos GonzÃ¡lez Izquierda Unida
## 5  consejal                  Antonio CÃ¡mara Eguinoa Partido Popular
## 6  consejal  Antonio Rey de ViÃ±as SÃ¡nchez-Majestad           CC OO
## 7  consejal                   Antonio Romero LÃ¡zaro            PSOE
## 8  consejal          Arturo Luis FernÃ¡ndez Ãlvarez            CEIM
## 9  consejal              BeltrÃ¡n GutiÃ©rrez Moliner Partido Popular
## 10 consejal                 CÃ¡ndido CerÃ³n Escudero Partido Popular
## ..      ...                                      ...             ...

glimpse(miembros) # parecido a str()
## Observations: 83
## Variables:
## $ funcion      (fctr) consejal, consejal, consejal, consejal, consejal...
## $ nombre       (fctr) Alberto Recarte GarcÃ­a Andrade, Alejandro Couce...
## $ organizacion (fctr) Partido Popular, CEIM, PSOE, Izquierda Unida, Pa...
```

#### Data table

**dplyr** permite trabajar con [**data tables**](http://datatable.r-forge.r-project.org/).

Pro:

-   *a priori* beneficiamos de la alta rapidez de las **data tables**
-   la sintaxis es mucho más simple que con el operador `[`

Contra:

-   para operaciones multiples (por ejemplo seleción + nueva variable), usar directamente las **data.table**s pueden ser más eficazes.
-   si buscamos rapidez pura, usaremos **data tables**

Convertimos los movimientos (77207 observaciones) en un objeto data table:

    ## 
    ## Attaching package: 'data.table'
    ## 
    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     between, last

#### Bases de datos

**dplyr** tambien puede trabajar con bases de datos de forma casi transparente:

-   SQLite
-   PostgreSQL/Redshift
-   MySQL/MariaDB
-   Bigquery
-   MonetDB

Más información [aquí](http://cran.r-project.org/web/packages/dplyr/vignettes/databases.html) (inglés).

### Los verbos

> "En el principio existía el Verbo"

-   `select()`: seleccionar columnas por nombre
-   `filter()`: suprimir las filas que no respectan una condición (+`slice()`: filtraje por posición)
-   `arrange()`: ordenar filas
-   `mutate()`: añade nuevas variables (con `group_by()`)
-   `summarise()`: agrupar valores (con `group_by()`)

¿como funciona?

-   primer argumento es una data.frame
-   los siguientes argumentos dicen que hacer con los datos
-   siempre devuelve otra data.frame

#### Seleccionar columnas con `select()`

Cuanto tenéis un objeto con muchas columnas, puede ser útil usar `select()` para reducir este número:

``` r
# todas las columnas menos 'funcion'
select(miembros, -funcion)
# las columnas entre 'nombre' y 'fecha'
select(movimientos, nombre:fecha)
# las columns con 'om'
select(movimientos, contains("om"))
# las columnas que empiezan por 'nom'
select(movimientos, starts_with("nom"))
# las columnas que respectan una expresión regular
select(movimientos, matches("?uto"))
```

> equivalente en SQL: `SELECT`

``` r
# guardamos esta versión simplifacada de 'movimientos' renombrando las columnas
mov <- select(movimientos, nom = nombre, imp =  importe, act = actividad)
```

#### Filtrar registros con `filter()`

`filter()` permite filtrar los registros. El primer argumento es el nombre del data frame. El segundo y los siguientes son expreciones logicas que serán evaluadas en el contexto del data frame:

``` r
filter(miembros, organizacion %in% c("PSOE", "Partido Popular"))
## Source: local data frame [42 x 3]
## 
##     funcion                                   nombre    organizacion
## 1  consejal          Alberto Recarte GarcÃ­a Andrade Partido Popular
## 2  consejal Ãngel Eugenio GÃ³mez del Pulgar Perales            PSOE
## 3  consejal                  Antonio CÃ¡mara Eguinoa Partido Popular
## 4  consejal                   Antonio Romero LÃ¡zaro            PSOE
## 5  consejal              BeltrÃ¡n GutiÃ©rrez Moliner Partido Popular
## 6  consejal                 CÃ¡ndido CerÃ³n Escudero Partido Popular
## 7  consejal    Rafael DarÃ­o FernÃ¡ndez Yruegas Moro Partido Popular
## 8  consejal    Estanislao RodrÃ­guez-Ponga Salamanca Partido Popular
## 9  consejal                  Fernando Serrano AntÃ³n Partido Popular
## 10 consejal             Francisco JosÃ© Moure Bourio Partido Popular
## ..      ...                                      ...             ...
filter(miembros, grepl("Antonio", nombre))
## Source: local data frame [4 x 3]
## 
##    funcion                                  nombre    organizacion
## 1 consejal                 Antonio CÃ¡mara Eguinoa Partido Popular
## 2 consejal Antonio Rey de ViÃ±as SÃ¡nchez-Majestad           CC OO
## 3 consejal                  Antonio Romero LÃ¡zaro            PSOE
## 4 consejal             JosÃ© Antonio Moral SantÃ­n Izquierda Unida
filter(movimientos, importe > 10000)
## Source: local data table [10 x 8]
## 
##                                  nombre      fecha hora minuto  importe
## 1  Ricardo Romero de Tejada y Picatoste 2007-11-26   10     59 11930.00
## 2       Ildefonso JosÃ© SÃ¡nchez Barcoj 2006-02-15    9     13 11000.00
## 3       Ildefonso JosÃ© SÃ¡nchez Barcoj 2009-12-31    2     40 16921.76
## 4              Miguel Blesa de la Parra 2006-04-05   16     51 12597.27
## 5              Miguel Blesa de la Parra 2006-07-20   14     50 13148.30
## 6                 RamÃ³n Ferraz Ricarte 2007-12-20   14      9 13549.00
## 7                     MatÃ­as Amat Roca 2006-12-27   17      6 15000.00
## 8                     MatÃ­as Amat Roca 2008-11-12   12     16 10400.00
## 9         Enrique de la Torre MartÃ­nez 2007-11-29    9     57 12000.00
## 10        Enrique de la Torre MartÃ­nez 2008-03-07   16      1 11075.00
## Variables not shown: comercio (chr), actividad_completa (chr), actividad
##   (fctr)
filter(movimientos, importe > 10000 & hora < 4)
## Source: local data table [1 x 8]
## 
##                            nombre      fecha hora minuto  importe
## 1 Ildefonso JosÃ© SÃ¡nchez Barcoj 2009-12-31    2     40 16921.76
## Variables not shown: comercio (chr), actividad_completa (chr), actividad
##   (fctr)
```

Para selecionar registros por posición, usar `slice()`:

``` r
slice(miembros, 50:55)
```

    ## Source: local data frame [6 x 3]
    ## 
    ##    funcion                       nombre    organizacion
    ## 1 consejal   Miguel Ãngel AbejÃ³n Resa             UGT
    ## 2 consejal Miguel Ãngel Araujo Serrano Partido Popular
    ## 3 consejal        Miguel Corsini Freese Partido Popular
    ## 4 consejal  Miguel MuÃ±iz de las Cuevas            PSOE
    ## 5 consejal         Pablo Abejas JuÃ¡rez Partido Popular
    ## 6 consejal           Pedro Bedia PÃ©rez           CC OO

> equivalente en SQL: `WHERE`

#### Sortear registros con `arrange()`

`arrange()` permite sortear los registros por una o varias columnas:

``` r
arrange(miembros, desc(organizacion), nombre)
```

    ## Source: local data frame [83 x 3]
    ## 
    ##     funcion                                   nombre organizacion
    ## 1  consejal                  Gonzalo MartÃ­n Pascual          UGT
    ## 2  consejal           JosÃ© Ricardo MartÃ­nez Castro          UGT
    ## 3  consejal               Miguel Ãngel AbejÃ³n Resa          UGT
    ## 4  consejal             Rafael Eduardo Torres Posada          UGT
    ## 5  consejal Ãngel Eugenio GÃ³mez del Pulgar Perales         PSOE
    ## 6  consejal                   Antonio Romero LÃ¡zaro         PSOE
    ## 7  consejal        Francisco JosÃ© PÃ©rez FernÃ¡ndez         PSOE
    ## 8  consejal                     Ignacio Varela DÃ­az         PSOE
    ## 9  consejal                  JoaquÃ­n GarcÃ­a Pontes         PSOE
    ## 10 consejal                      Jorge GÃ³mez Moreno         PSOE
    ## ..      ...                                      ...          ...

`top_n` es una combinación de sorteo + filtro:

``` r
top_n(mov, 2, imp)
## Source: local data table [2 x 3]
## 
##                               nom      imp           act
## 1 Ildefonso JosÃ© SÃ¡nchez Barcoj 16921.76 COMPRA BIENES
## 2               MatÃ­as Amat Roca 15000.00         HOGAR
top_n(miembros, 1) # por defecto, ordena por la ultima columna
## Selecting by organizacion
## Source: local data frame [4 x 3]
## 
##    funcion                         nombre organizacion
## 1 consejal   Rafael Eduardo Torres Posada          UGT
## 2 consejal        Gonzalo MartÃ­n Pascual          UGT
## 3 consejal JosÃ© Ricardo MartÃ­nez Castro          UGT
## 4 consejal     Miguel Ãngel AbejÃ³n Resa          UGT
```

> equivalente en SQL: `ORDER BY`

#### Agregar y transformar con `group_by`, `summarise()` y `mutate()`

`summarise()` agrega los datos por groupos creados por `group_by()`. Si no estan agrupados, agrupa todo en un solo registro.

``` r
summarise(movimientos, max(importe))
## Source: local data table [1 x 1]
## 
##   max(importe)
## 1     16921.76
summarise(group_by(mov, nom), max_personal = max(imp))
## Source: local data table [83 x 2]
## 
##                                         nom max_personal
## 1           Alberto Recarte GarcÃ­a Andrade      3509.20
## 2                  Alejandro Couceiro Ojeda      1150.00
## 3  Ãngel Eugenio GÃ³mez del Pulgar Perales      4906.00
## 4                  Angel Rizaldos GonzÃ¡lez       843.00
## 5                   Antonio CÃ¡mara Eguinoa      2742.00
## 6   Antonio Rey de ViÃ±as SÃ¡nchez-Majestad      1751.08
## 7                    Antonio Romero LÃ¡zaro      4500.00
## 8           Arturo Luis FernÃ¡ndez Ãlvarez      2550.00
## 9               BeltrÃ¡n GutiÃ©rrez Moliner      2439.50
## 10                 CÃ¡ndido CerÃ³n Escudero      2800.00
## ..                                      ...          ...
summarise(group_by(miembros, organizacion), n())
## Source: local data frame [11 x 2]
## 
##            organizacion n()
## 1                        19
## 2                 CC OO   6
## 3                  CEIM   3
## 4                  CEOE   1
## 5  ComisiÃ³n de Control   1
## 6      Conf. de Cuadros   1
## 7       Izquierda Unida   5
## 8       Partido Popular  27
## 9    Patronal (Unipyme)   1
## 10                 PSOE  15
## 11                  UGT   4
```

`mutate()` es muy similar. La diferencia es que `mutate()` no dismimue el número de filas pero añade columnas con el resultado de la agregación:

``` r
mutate(mov, total = sum(imp))
## Source: local data table [77,202 x 4]
## 
##                                nom    imp           act    total
## 1  Alberto Recarte GarcÃ­a Andrade  38.70          ROPA 11806773
## 2  Alberto Recarte GarcÃ­a Andrade  14.60         HOTEL 11806773
## 3  Alberto Recarte GarcÃ­a Andrade  95.62   RESTAURANTE 11806773
## 4  Alberto Recarte GarcÃ­a Andrade  49.13         COCHE 11806773
## 5  Alberto Recarte GarcÃ­a Andrade  13.94         COCHE 11806773
## 6  Alberto Recarte GarcÃ­a Andrade  80.00         HOTEL 11806773
## 7  Alberto Recarte GarcÃ­a Andrade  53.37         COCHE 11806773
## 8  Alberto Recarte GarcÃ­a Andrade  42.00 COMPRA BIENES 11806773
## 9  Alberto Recarte GarcÃ­a Andrade 263.30   RESTAURANTE 11806773
## 10 Alberto Recarte GarcÃ­a Andrade   2.10         COCHE 11806773
## ..                             ...    ...           ...      ...
mutate(group_by(mov, nom), total_personal = sum(imp), pp = imp/total_personal)
## Source: local data table [77,202 x 5]
## 
##                                nom    imp           act total_personal
## 1  Alberto Recarte GarcÃ­a Andrade  38.70          ROPA         136504
## 2  Alberto Recarte GarcÃ­a Andrade  14.60         HOTEL         136504
## 3  Alberto Recarte GarcÃ­a Andrade  95.62   RESTAURANTE         136504
## 4  Alberto Recarte GarcÃ­a Andrade  49.13         COCHE         136504
## 5  Alberto Recarte GarcÃ­a Andrade  13.94         COCHE         136504
## 6  Alberto Recarte GarcÃ­a Andrade  80.00         HOTEL         136504
## 7  Alberto Recarte GarcÃ­a Andrade  53.37         COCHE         136504
## 8  Alberto Recarte GarcÃ­a Andrade  42.00 COMPRA BIENES         136504
## 9  Alberto Recarte GarcÃ­a Andrade 263.30   RESTAURANTE         136504
## 10 Alberto Recarte GarcÃ­a Andrade   2.10         COCHE         136504
## ..                             ...    ...           ...            ...
## Variables not shown: pp (dbl)
```

> equivalente en SQL: `GROUP BY`

### los 'pipes' (tubos)

El operador `%>%` permite encadenar los verbos y escribir un codigo más legible.

`data %>% function(parameters)` es equivalente a: `funcion(data, parameters)`

Por ejemplo,

``` r
top_n(
  arrange(
   summarize(
      group_by(
          filter(movimientos, importe > 0)
          , nombre)
        , total = sum(importe)
      )
    , desc(total)
    )
  , 10
  )
```

es equivalente a:

``` r
# top 10 miembros con más gastos
movimientos %>%
  group_by(nombre) %>%
  summarize(total = sum(importe)) %>%
  arrange(desc(total)) %>%
  top_n(10)
```

### Ejercicios

#### ¿cual es el import maximo por miembros?

Dos maneras. O via un `summarize`:

``` r
movimientos %>% 
  group_by(nombre) %>%
  summarize(gasto_max = max(importe))
```

    ## Source: local data table [83 x 2]
    ## 
    ##                                      nombre gasto_max
    ## 1           Alberto Recarte GarcÃ­a Andrade   3509.20
    ## 2                  Alejandro Couceiro Ojeda   1150.00
    ## 3  Ãngel Eugenio GÃ³mez del Pulgar Perales   4906.00
    ## 4                  Angel Rizaldos GonzÃ¡lez    843.00
    ## 5                   Antonio CÃ¡mara Eguinoa   2742.00
    ## 6   Antonio Rey de ViÃ±as SÃ¡nchez-Majestad   1751.08
    ## 7                    Antonio Romero LÃ¡zaro   4500.00
    ## 8           Arturo Luis FernÃ¡ndez Ãlvarez   2550.00
    ## 9               BeltrÃ¡n GutiÃ©rrez Moliner   2439.50
    ## 10                 CÃ¡ndido CerÃ³n Escudero   2800.00
    ## ..                                      ...       ...

O usando `filter`:

``` r
movimientos %>% 
  group_by(nombre) %>%
  filter(importe == max(importe))
```

    ## Source: local data table [85 x 8]
    ## Groups: nombre
    ## 
    ##                                      nombre      fecha hora minuto importe
    ## 1           Alberto Recarte GarcÃ­a Andrade 2008-12-12   20      6 3509.20
    ## 2                  Alejandro Couceiro Ojeda 2004-12-16   15     34 1150.00
    ## 3  Ãngel Eugenio GÃ³mez del Pulgar Perales 2008-07-26   11     26 4906.00
    ## 4                  Angel Rizaldos GonzÃ¡lez 2006-07-15   16     13  843.00
    ## 5                   Antonio CÃ¡mara Eguinoa 2008-08-16   14     51 2742.00
    ## 6   Antonio Rey de ViÃ±as SÃ¡nchez-Majestad 2005-08-02   16     57 1751.08
    ## 7                    Antonio Romero LÃ¡zaro 2007-11-30    9     57 4500.00
    ## 8           Arturo Luis FernÃ¡ndez Ãlvarez 2011-12-23    0     51 2550.00
    ## 9               BeltrÃ¡n GutiÃ©rrez Moliner 2011-05-30   11     43 2439.50
    ## 10                 CÃ¡ndido CerÃ³n Escudero 2010-07-16   12     46 2800.00
    ## ..                                      ...        ...  ...    ...     ...
    ## Variables not shown: comercio (chr), actividad_completa (chr), actividad
    ##   (fctr)

En este 2º metodo, si hay dos gastos iguales que son los gatos maximos, salen los dos.

#### ¿cual es el perfil horario de las compras?

Truco: la función `n()` permite dentro `summarise()`, `mutate()` y `filter()` contar el numéro de registros.

Respuesta:

``` r
res <- movimientos %>%
  group_by(hora) %>%
  summarise(total = sum(importe))

library(ggplot2)
ggplot(res, aes(x=hora, y=total))+geom_bar(stat="identity")
```

![](tutorial-dplyr_files/figure-markdown_github/unnamed-chunk-19-1.png)

#### ¿cual son las 10 actividades más frecuentes?

Respuesta:

``` r
res <- movimientos %>%
  group_by(actividad) %>%
  summarise(n = n()) %>%
  top_n(10)
## Selecting by n

res$actividad <- reorder(res$actividad, res$n)

ggplot(arrange(res, n), aes(x=actividad, y=n)) +
  geom_bar(stat="identity") + 
  coord_flip()
```

![](tutorial-dplyr_files/figure-markdown_github/unnamed-chunk-20-1.png)

#### ¿quien es miembros con mejor apetito?

Respuesta:

``` r
movimientos %>%
  filter(actividad == "RESTAURANTE") %>%
  group_by(nombre) %>%
  summarise(total_gastro = sum(importe)) %>%
  top_n(1)
## Selecting by total_gastro
## Source: local data table [1 x 2]
## 
##                              nombre total_gastro
## 1 Guillermo Ricardo Marcos Guerrero     114908.8
```

#### ¿para cada uno de los 10 miembros más despilfarradores, en que actividad han gastado más? ¿y cuanto?

Truco: juntar datos con las funciones `left_join`,`right_join`, `semi_join`... sintaxis: `left_join(x, y, by = NULL, copy = FALSE, ...)`

Respuesta:

``` r
# los 10 miembros con más gastos
despilfarradores <- movimientos %>%
  group_by(nombre) %>%
  summarize(total = sum(importe)) %>%
  arrange(desc(total)) %>%
  top_n(10)
## Selecting by total

left_join(despilfarradores, movimientos) %>%
  group_by(nombre, actividad_completa) %>%
  summarise(total_actividad = sum(importe)) %>%
  top_n(1)
## Joining by: "nombre"
## Selecting by total_actividad
## Source: local data table [10 x 3]
## Groups: nombre
## 
##                               nombre
## 1      Enrique de la Torre MartÃ­nez
## 2    Ildefonso JosÃ© SÃ¡nchez Barcoj
## 3        JosÃ© Antonio Moral SantÃ­n
## 4       Juan Manuel Astorqui Portera
## 5  Maria Mercedes de la Merced Monge
## 6              Mariano PÃ©rez Claver
## 7                  MatÃ­as Amat Roca
## 8           Miguel Blesa de la Parra
## 9              RamÃ³n Ferraz Ricarte
## 10           Ricardo Morado Iglesias
## Variables not shown: actividad_completa (chr), total_actividad (dbl)
```

#### ¿el tipo de gasto depiende del partido político?

Respuesta:

``` r
all <- left_join(tbl_df(movimientos), miembros, by="nombre")

res <- all %>% filter(!is.na(actividad) & actividad != '' & organizacion %in% c("Izquierda Unida", "Partido Popular", "PSOE")) %>%
  group_by(organizacion, actividad) %>%
  summarise(total = sum(importe))

ggplot(res, aes(x=actividad, y=total, fill=organizacion)) +
  geom_bar(stat="identity", position = "fill") + 
  coord_flip()
```

![](tutorial-dplyr_files/figure-markdown_github/unnamed-chunk-23-1.png)

si normalisamos el dinero recibido por partido:

``` r
res <- res %>%
  filter(total > 50000) %>%
  group_by(organizacion) %>%
  mutate(total_partido = sum(total))

#to to: normalisar
ggplot(res, aes(x=actividad, y=total/total_partido, fill=organizacion)) +
  geom_bar(stat="identity") + 
  coord_flip()
```

![](tutorial-dplyr_files/figure-markdown_github/unnamed-chunk-24-1.png)

Repartición por función:

``` r
res <- all %>% filter(!is.na(actividad)) %>%
  group_by(funcion, actividad) %>%
  summarise(total = sum(importe)) %>%
  arrange(desc(total))

ggplot(res, aes(x=actividad, y=total, fill=funcion)) +
  geom_bar(stat="identity", position = "fill") + 
  coord_flip()
```

![](tutorial-dplyr_files/figure-markdown_github/unnamed-chunk-25-1.png)

#### mapa del gasto por dia de la semana y persona

``` r
despilfarradores <- movimientos %>%
  group_by(nombre) %>%
  mutate(total = sum(importe)) %>%
  filter(dense_rank(-total) < 10)

res <- ungroup(despilfarradores) %>%
  group_by(nombre, dia = strftime(fecha, format = "%w-%a")) %>%
  summarise(gasto = sum(importe)) 
summary(res)
```

    ##                              nombre       dia                gasto       
    ##  Enrique de la Torre MartÃ­nez  : 7   Length:63          Min.   :  3924  
    ##  Ildefonso JosÃ© SÃ¡nchez Barcoj: 7   Class :character   1st Qu.: 35660  
    ##  JosÃ© Antonio Moral SantÃ­n    : 7   Mode  :character   Median : 57848  
    ##  Juan Manuel Astorqui Portera   : 7                      Mean   : 57966  
    ##  Mariano PÃ©rez Claver          : 7                      3rd Qu.: 75001  
    ##  MatÃ­as Amat Roca              : 7                      Max.   :117559  
    ##  (Other)                        :21

``` r
myPalette <- colorRampPalette(rev(RColorBrewer::brewer.pal(11, "Spectral")), space="Lab")

ggplot(data = res, aes(x = nombre, y = dia, fill = gasto)) +
  geom_tile() +
  scale_fill_gradientn(colours = myPalette(100)) +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

![](tutorial-dplyr_files/figure-markdown_github/unnamed-chunk-26-1.png)

### do()

`do()` permite hacer operaciones por grupos de datos. Estas operaciones pueden devolver dataframes o lista de objetos. Es particularemente util para trabajar con modelos.

#### Tendencia horaria

``` r
modelos <- all %>% 
            filter(organizacion %in% c("Izquierda Unida", "Partido Popular", "PSOE")) %>%
            mutate(hora_num = hora+minuto/60) %>%
            group_by(organizacion) %>%
            do(mod = lm(importe ~ hora_num, data = .))
modelos
```

    ## Source: local data frame [3 x 2]
    ## Groups: <by row>
    ## 
    ##      organizacion     mod
    ## 1 Izquierda Unida <S3:lm>
    ## 2 Partido Popular <S3:lm>
    ## 3            PSOE <S3:lm>

``` r
# extraimos los coeficientes
modelos %>%
  rowwise %>%
  do(data.frame(
     grupo = .[[1]],
     var = names(coef(.$mod)),
     coef(summary(.$mod))
  ))
```

    ## Source: local data frame [6 x 6]
    ## Groups: <by row>
    ## 
    ##             grupo         var    Estimate Std..Error   t.value
    ## 1 Izquierda Unida (Intercept)  31.1850220  8.7582983  3.560626
    ## 2 Izquierda Unida    hora_num   7.1220507  0.5291919 13.458351
    ## 3 Partido Popular (Intercept) 123.3416734  5.4961757 22.441363
    ## 4 Partido Popular    hora_num  -0.5988676  0.3438199 -1.741806
    ## 5            PSOE (Intercept) 114.9967125  4.8625197 23.649614
    ## 6            PSOE    hora_num  -1.1319605  0.2969374 -3.812119
    ## Variables not shown: Pr...t.. (dbl)

### Windows functions

Estas funciones toman `n` argumentos para devolver `n` valores:

-   **lag** / **lead**
-   **min\_rank** / **dense\_rank** / **ntiles** / ...
-   cumulative
-   rolling (aún no esta implementado, usar `data.table`)

#### movimientos separados por menos de 5 minutos de personas diferentes

``` r
# añadimos un campo "datetime"
all$t <- as.POSIXct(as.numeric(all$fecha) + all$hora*60*60 + all$minuto*60, origin="1970-01-01")

all %>% arrange(t) %>%
  filter(as.numeric(t -lag(t)) < 5*60, nombre != lag(nombre))
```

    ## Source: local data frame [26,940 x 11]
    ## 
    ##                               nombre      fecha hora minuto importe
    ## 1           Miguel Blesa de la Parra 2003-01-01   11      2   64.25
    ## 2  Carlos MarÃ­a MartÃ­nez MartÃ­nez 2003-01-01   11      3  376.00
    ## 3       Francisco JosÃ© Moure Bourio 2003-01-01   11     13   48.08
    ## 4       Ignacio de Navasques CobiÃ¡n 2003-01-01   11     13   42.00
    ## 5   Ignacio del RÃ­o GarcÃ­a de Sola 2003-01-01   11     13   50.00
    ## 6                JosÃ© Acosta Cubero 2003-01-01   11     13   94.78
    ## 7                Pedro Bugidos Garay 2003-01-01   11     13   34.80
    ## 8                  RubÃ©n Cruz Orive 2003-01-01   11     13  110.00
    ## 9                  RubÃ©n Cruz Orive 2003-01-01   11     13  148.00
    ## 10                 RubÃ©n Cruz Orive 2003-01-01   11     13   75.26
    ## ..                               ...        ...  ...    ...     ...
    ## Variables not shown: comercio (chr), actividad_completa (chr), actividad
    ##   (fctr), funcion (fctr), organizacion (fctr), t (time)

### Misc

#### Evolution temporal

``` r
library(xts)
library(dygraphs)
ts <- movimientos_dt %>% group_by(fecha) %>% summarise(total=sum(importe))
ts <- xts(ts$total, order.by=ts$fecha)

dygraph(ts) %>% 
  dyRoller(rollPeriod = 30) %>%
  dyRangeSelector()
```
