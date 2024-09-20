Prácticas de aula (PA05): Medio físico, variables ambientales,
correlación y regresión<small><br>Biogeografía (GEO-131)<br>Universidad
Autónoma de Santo Domingo (UASD)<br>Semestre 2024-02</small>
================
El Tali
2024-09-20

Versión HTML (quizá más legible),
[aquí](https://biogeografia-master.github.io/medio-fisico-variables-ambientales/README.html)

# Fecha/hora de entrega

**25 de septiembre de 2024, 11:59 pm.**

# Introducción

## “Plantilla geográfica”, medio físico, variables ambientales, correlación, regresión

En el contexto de esta asignatura, puedes asumir que estos nombres se
refieren a lo mismo: “plantilla geográfica”, medio físico, variables
ambientales. Todo ecosistema tiene una “parte dura” (que puede ser
relativamente estática o dinámica) sobre o dentro de la cual ocurren las
interacciones entre especies que conforman nuestra comunidad ecológica.
Todos los lugares del planeta disponen, en la actualidad, de alguna
forma de información. El objetivo es poder reunir variables de esa
“parte dura”, y organizarlas en una forma matricial, donde las filas
sean “sitios” y las columnas sean “variables”; a este producto le
llamaremos “matriz ambiental”.

Dado que es común que las variables ambientales se correlacionen entre
sí, en este ejercicio también es importante identificar la magnitud y
dirección de estas correlaciones, ya que pueden influir en la
interpretación de los datos y en los resultados de los análisis
posteriores. Por ejemplo, una alta correlación entre variables puede
indicar redundancia, lo que significa que estas variables están
capturando información similar sobre el medio físico. Esto puede llevar
a problemas como la multicolinealidad en modelos de regresión, lo que
afecta la estabilidad de los coeficientes estimados y su interpretación.
Por tanto, evaluar la correlación entre variables es un paso clave para
decidir cuáles deben ser incluidas en un análisis más profundo y cuáles
pueden ser excluidas o transformadas para mejorar la robustez del
modelo.

En cuanto a la forma de almacenar los datos sobre variables ambientales,
remarco lo estrictamente necesario que debes saber sobre los modelos de
datos en información geográfica. Aunque también hay información en el
modelo de datos vectorial, la mayor parte de la información del medio
físico disponible en repositorios globales de información geoespacial
(uno que centraliza mucha información es Google Earth Engine, pero hay
varios), se encuentra almacenadas en archivos del modelo de datos
ráster. En el caso más simple, un ráster es una matriz de $n$ filas por
$m$ columnas, que forman celdas distribuidas de forma regular siguiendo
un sistema de coordenadas de referencia, donde cada celda representa el
valor de una variable sobre el territorio. Una realización muy popular
de los rásters son las “fotos” que capturas con un móvil. No obstante,
los rásters que informan sobre el medio físico pueden complicarse “todo
lo que queramos”. **En un ráster, sólo se almacenan valores numéricos,
pero la variable representada puede ser cualitativa o cuantitativa**.

Si la variable es cuantitativa, los valores en el ráster se almacenarán
de acuerdo a un factor de escala y a un *offset* o desplazamiento. Por
ejemplo, supongamos que tienes un valor de temperatura para la capa
bioclimática **bio1 (mean annual air temperature)** o **temperatura
media anual del aire**, y deseas interpretarlo correctamente en °C.

> **Ejemplo:**
>
> Supón que el valor registrado para un punto en la capa bioclimática
> **bio1** es **2900**.
>
> 1.  **Unidad y factor de escala:** El factor de escala es **0.1**, lo
>     que significa que el valor debe multiplicarse por **0.1** para
>     obtener la temperatura en Kelvin.
>
> - 2900 × 0.1 = 290 Kelvin
>
> 2.  **Offset, ajuste o desplazamiento:** Según la tabla, el *offset*
>     es **-273.15**, lo cual convierte el valor de Kelvin a grados
>     Celsius.
>
> - 290 Kelvin - 273.15 = 16.85 °C
>
> **Interpretación:** El valor final, **16.85 °C**, representa la
> temperatura media anual del aire para ese punto geográfico, calculada
> como el promedio diario a lo largo de un año.

Por otro lado , si la variable es cualitativa, el ráster de todas formas
almacenará valores numéricos, que son realmente códigos representados
por enteros positivos (números naturales). En este caso, dispondrás de
una tabla de equivalencias para determinar a qué clase corresponde cada
código.

## Fuentes disponibles para este ejercicio

La mayor parte de las fuentes de este ejercicio son ráster. El único
vectorial es un límite de tierra firme de República Dominicana, de GADM
(investiga qué es GADM). El resto son rásters casi todos recortados para
el territorio emergido de República Dominicana.

Todos los archivos se localizan en el directorio `compartidos`,
subdirectorio `geo131-pa05` en tu cuenta del servidor RStudio.

¿Qué significan los valores en las celdas de cada ráster? Tres
*dataset*, `CGIAR-ELEVACION`, `CHELSA*` (este es un conjunto de
archivos) y `YINSOLTIME*`, representan variables cuantitativas medidas
en números reales. Los otros dos conjuntos, `PROBAV*` y
`G90-GEOMORFONOS`, representan variables cualitativas medidas
nominalmente. Desde el repositorio [Estadística zonal multipropósito
sobre información geoespacial de República Dominicana, usando Google
Earth Engine, Python y R](https://github.com/geofis/zonal-statistics)
(Martínez-Batlle 2022) podrás enlazar con las fuentes bibliográficas de
cada una de las variables usadas en esta práctica. A continuación te
dejo enlaces a notas específicas de estas fuentes:

- `CGIAR-ELEVACION.tif`. **RÁSTER CUANTITATIVO**. El nombre es bastante
  explícito. Datos de elevación (huecos rellenos), de la Misión
  Topográfica del Transbordador Shuttle (SRTM) para el globo, versión 4,
  de la base de datos del CGIAR-CS.

- `CHELSA*.tif`. **RÁSTERS CUANTITATIVOS**. Se compone de 19 rásters
  individuales, correspondientes a igual número de variables
  bioclimáticas que podrás consultar en la tabla 7.1 de la página 11 de
  [este
  PDF](https://chelsa-climate.org/wp-admin/download-page/CHELSA_tech_specification_V2.pdf).
  En la tabla, también podrás consultar el factor de escala y el
  desplazamiento que deberás aplicar para obtener sus valores en las
  unidades correspondientes.

- `PROBAV*.tif`. **RÁSTERS CUALITATIVOS**. Cinco rásters de cobertura
  del suelo del programa Copernicus, cada uno correspondiente a un año
  entre 2015 y 2019. Consulta la leyenda de la tabla 4 de [este
  PDF](https://zenodo.org/records/4723921/files/CGLOPS1_PUM_LC100m-V3_I3.4.pdf?download=1)

- `G90-GEOMORFONOS.tif`. **RÁSTER CUALITATIVO**. “Geomórfonos” del
  conjunto de datos “Geomorpho90m”, que son un conjunto de elementos
  morfológicos generales obtenidos a partir de aplicar algoritmos de
  vecindad a un modelo digital de elevaciones. [Esta es la fuente
  original](https://doi.pangaea.de/10.1594/PANGAEA.899135), y fue
  producida con el addon de GRASS GIS
  [`r.geomorphon`](https://grass.osgeo.org/grass84/manuals/r.geomorphon.html),
  donde podrás consultar la leyenda (esto último, muy importante). Una
  leyenda alternativa en español es esta:

| Código |            Morfología             |
|:------:|:---------------------------------:|
|   1    |              llanura              |
|   2    |               pico                |
|   3    | cresta (interfluvio no inclinado) |
|   4    |             hombrera              |
|   5    |   gajo (interfluvio inclinado)    |
|   6    |             vertiente             |
|   7    |              vaguada              |
|   8    |            piedemonte             |
|   9    |               valle               |
|   10   |          depresión, sima          |

- `G90-PENDIENTE.tif`. **RÁSTER CUANTITATIVO**. El nombre es bastante
  explícito. Datos de pendiente (en grados), generados a partir del
  modelo digital de elevaciones de la Misión Topográfica del
  Transbordador Shuttle (SRTM) para el globo.

- `YINSOLTIME_horas_insolacion_topografica.tif`. **RÁSTER
  CUANTITATIVO**. Horas de insolación anuales de manera directa, donde
  el factor de escala es 1 y el *offset* es 0. Usando el modelo digital
  de elevaciones CGIAR, también citado en el repo de estadística zonal
  (Martínez-Batlle 2022), se estimaron las horas de insolación directa
  por año, condicionadas por la topografía. Para esto, se utilizó el
  [complemento `r.sun` de GRASS
  GIS](https://grass.osgeo.org/grass84/manuals/r.sun.html).

``` r
tipos_var <- data.frame(
  Fuente = list.files('~/compartidos/geo131-pa05', pattern = '*tif', full.names = T)) %>% 
  mutate(Fuente = gsub('/home/jose', '~', Fuente)) %>% 
  mutate(Tipo = ifelse(grepl('PROBA|G90-GEOM', Fuente), 'Cualitativa', 'Cuantitativa'))
tipos_var %>% 
  knitr::kable()
```

| Fuente                                                                | Tipo         |
|:----------------------------------------------------------------------|:-------------|
| ~/compartidos/geo131-pa05/CGIAR-ELEVACION.tif                         | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio1_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio10_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio11_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio12_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio13_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio14_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio15_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio16_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio17_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio18_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio19_1981-2010_V21.tif              | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio2_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio3_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio4_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio5_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio6_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio7_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio8_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/CHELSA_bio9_1981-2010_V21.tif               | Cuantitativa |
| ~/compartidos/geo131-pa05/G90-GEOMORFONOS.tif                         | Cualitativa  |
| ~/compartidos/geo131-pa05/G90-PENDIENTE.tif                           | Cuantitativa |
| ~/compartidos/geo131-pa05/PROBAV_2015.tif                             | Cualitativa  |
| ~/compartidos/geo131-pa05/PROBAV_2016.tif                             | Cualitativa  |
| ~/compartidos/geo131-pa05/PROBAV_2017.tif                             | Cualitativa  |
| ~/compartidos/geo131-pa05/PROBAV_2018.tif                             | Cualitativa  |
| ~/compartidos/geo131-pa05/PROBAV_2019.tif                             | Cualitativa  |
| ~/compartidos/geo131-pa05/YINSOLTIME-horas_insolacion_topografica.tif | Cuantitativa |

Con fines demostrativos, voy a representar un mapa de coberturas con `R`
usando la fuente de PROBA-V. Primero cargaré paquetes, y luego haré la
correspondiente representación. Puedes usar el código a continuación en
un *script* de R para visualizar esta fuente.

> No tienes obligación de ejecutar este bloque código y el siguiente
> para representar el mapa. Sólo tiene fines demostrativos para
> mostrarte las capacidades de `leaflet`, una biblioteca muy útil para
> mapas interactivos.

``` r
library(tidyverse)
library(leaflet)
library(leaflet.extras)
library(webshot2)
library(htmlwidgets)
library(raster)
library(kableExtra)
library(sf)
```

A continuación representaré el mapa (si estás viendo la versión HTML del
documento, podrás interactuar con el mapa).

``` r
# Leer el archivo CLR
clr_data <- read.table("~/compartidos/geo131-pa05/PROBAV_2015.clr", header = FALSE)

# Extraer los valores, colores y nombres de clase
clase_valores <- clr_data$V1
colores_rgb <- rgb(clr_data$V2, clr_data$V3, clr_data$V4, maxColorValue = 255)
nombres_clases <- clr_data$V6

# Crear una paleta de colores personalizada basada en el CLR
paleta_colores <- colorFactor(palette = colores_rgb, domain = clase_valores)

# Cargar el archivo ráster
raster_archivo <- raster("~/compartidos/geo131-pa05/PROBAV_2015.tif")

# Verifica que los valores del ráster coincidan con los valores de clase del CLR
# print(unique(values(raster_archivo)))

# Crear un mapa de Leaflet con el ráster
mapa <- leaflet() %>%
  addTiles() %>%
  addRasterImage(raster_archivo, colors = paleta_colores, opacity = 0.8) %>%
  addLegend(position = "bottomright", 
            pal = paleta_colores, 
            values = clase_valores, 
            labFormat = labelFormat(transform = function(x) nombres_clases[match(x, clase_valores)]),
            title = "Clases del Ráster")


# Evaluar el formato de salida y tomar acción en función de él
if (grepl('gfm', output_format)) {
  # Si la salida es Markdown (GitHub-Flavored Markdown), guarda el mapa como HTML temporalmente
  html_file <- tempfile(fileext = ".html")
  saveWidget(mapa, file = html_file, selfcontained = TRUE)
  
  # Toma una captura del archivo HTML
  tmp_file <- tempfile(fileext = ".png")
  webshot2::webshot(url = html_file, file = tmp_file, vwidth = 800, vheight = 600)
  
  # Incluye la imagen capturada en el documento
  knitr::include_graphics(tmp_file)
} else {
  # Si la salida es HTML, imprime el objeto mapa normalmente
  mapa
}
```

<img src="../../../../../../tmp/RtmpgM6zJJ/file9b31614840a37.png" width="100%" />

# Instrucciones generales que aplican a todos los ejercicios.

1.  **La instrucción más importante: tu práctica me la entregarás en
    papel fotografiado (es preferible un PDF con todas las páginas
    juntas, hay aplicaciones móviles para esto) y me la puedes enviar
    por mensaje directo a través del foro. `R` sólo lo usarás para hacer
    cálculos.**

2.  Como se supone que esta práctica está pensada para hacerse en el
    aula, el servidor sólo será un apoyo, y deberías poder operarlo
    desde el teléfono. Por esta razón, la he diseñado de manera que sólo
    tengas que copiar código desde este cuaderno y pegarlo en un script
    de R (en el servidor, `File>New>R Script`). NO tienes que crear un
    proyecto de RStudio. Si hay algún proyecto abierto al momento de
    comenzar la práctica, ciérralo (`File>Close Project`). En el código
    que dejo de ayuda en cada ejercicio, tendrás que cambiar sólo
    algunas partes; busca siempre la indicación `#<-----Atender aquí`.

3.  Al final de cada ejercicio, debes escribir, a mano, un párrafo de
    mínimo cuatro oraciones, explicando por qué hiciste lo que hiciste
    (oración 1), cómo lo hiciste (oración 2), qué obtuviste (oración 3)
    y qué interpretas (oración 4).

4.  Elige un número entero entre el 2 y el 20 (anúncialo en el foro,
    para evitar duplicidad; el 1 se lo reserva el tali), para los
    ejercicios de aleatorización, el cual asignarás al objeto
    `mi_aleatorizacion`.

``` r
mi_aleatorizacion <-  #<-----Atender aquí
```

5.  Carga estos paquetes. Si te aparecen errores sobre que esta o
    aquella función no fue encontrada, o que el operador “%\>%” no fue
    encontrado, carga estas funciones.

``` r
library(sf)
library(raster)
library(tidyverse)
library(leaflet)
library(leaflet.extras)
library(webshot2)
library(htmlwidgets)
library(raster)
library(kableExtra)
```

# Ejercicio 1. Genera puntos aleatorios

Primero genera tus 10 puntos aleatorios.

``` r
# Generar 10 puntos aleatorios dentro del polígono de República Dominicana
rd <- st_read('~/compartidos/geo131-pa05/RD_GADM.gpkg', quiet = T) # Investiga qué es GADM
# Para asegurar reproducibilidad. Seleccionar desde "{" hasta "}", ambas inclusive, y ejecutar
{set.seed(mi_aleatorizacion)
  puntos <- st_as_sf(st_sample(rd, size = 10, type = "random"))}
```

Coloca en una tabla como la de abajo, las coordenadas de longitud y
latitud de cada punto en grados decimales (e.g. 19.1223); no olvides el
signo de la coordenada. Para simplificar los cálculos, redondea a 4
posiciones decimales (la precisión posicional es ~10 metros).

|  Punto  | Longitud | Latitud |
|:-------:|:--------:|:-------:|
| Punto 1 | xx.xxxx  | xx.xxxx |

Para generar dicha tabla, puedes usar el siguiente bloque de código:

``` r
puntos %>%
  mutate(Punto = paste('Punto', 1:nrow(puntos)),
         Longitud = round(st_coordinates(x)[,1], 4),
         Latitud = round(st_coordinates(x)[,2], 4)) %>%
  st_drop_geometry() %>% 
  as.data.frame() %>% 
  knitr::kable(align = 'c')
```

Por ejemplo, los puntos asignados al tali se verían así en la tabla:

|  Punto   | Longitud | Latitud |
|:--------:|:--------:|:-------:|
| Punto 1  | -71.0265 | 19.0706 |
| Punto 2  | -68.6605 | 18.4166 |
| Punto 3  | -68.6967 | 18.4075 |
| Punto 4  | -69.6880 | 18.6527 |
| Punto 5  | -71.2456 | 18.6809 |
| Punto 6  | -71.3539 | 17.9260 |
| Punto 7  | -70.5899 | 19.1120 |
| Punto 8  | -69.3622 | 19.2484 |
| Punto 9  | -70.6049 | 19.4888 |
| Punto 10 | -71.2229 | 18.8274 |

Ahora representa tus puntos con estas líneas de abajo. Justo debajo del
bloque de código, verás el mapa de los puntos del tali. Si la
distribución de tus puntos se parece a la de los del tali, algo anda
mal, pues no deberían coincidir. \> Representa tus puntos en papel
usando tu mapa, dibujando la silueta de RD; la precisión posicional no
es un requisito.

``` r
plot(rd %>% st_as_sfc()) # Representar el vectorial de RD
plot(puntos, add = T) # Para representar tus puntos
```

<img src="README_files/figure-gfm/unnamed-chunk-13-1.png" width="100%" />

> No olvides tu párrafo al final del ejercicio.

# Ejercicio 2. Extrae los valores para varios rásters

Se te asignan cinco variables cuantitativas, según tu número elegido.
Usa este código para generarlas.

``` r
{set.seed(mi_aleatorizacion)
  fuentes_cuan_sel <- c(
    sample(tipos_var[grepl('G90-PE|CGIAR|YINS', tipos_var$Fuente), 'Fuente'], 2),
    sample(tipos_var[grepl('CHELSA', tipos_var$Fuente), 'Fuente'], 3))}
cat('Estos fueron mis archivos seleccionados', fuentes_cuan_sel, sep = '\n')
```

Por ejemplo, al tali le salieron estas variables (si te salieron las
mismas, algo anda mal):

    ## Estos fueron mis archivos seleccionados
    ## ~/compartidos/geo131-pa05/CGIAR-ELEVACION.tif
    ## ~/compartidos/geo131-pa05/G90-PENDIENTE.tif
    ## ~/compartidos/geo131-pa05/CHELSA_bio15_1981-2010_V21.tif
    ## ~/compartidos/geo131-pa05/CHELSA_bio1_1981-2010_V21.tif
    ## ~/compartidos/geo131-pa05/CHELSA_bio10_1981-2010_V21.tif

Usando tus puntos creados en el ejercicio anterior, extrae los valores
correspondientes de cada ráster para cada uno de tus 10 puntos. Con el
código de abajo lo podrás hacer. NECESARIO: investiga sobre las
funciones `*apply` y los bucles `for`; te mostraré cómo extraer hacer la
extracción de valores desde tus cinco ráster de manera masiva, tanto con
`*apply` como con bucle `for`.

- Con `sapply`, que forma parte de la familia de los `*apply`:

``` r
df_valores_cuan_sel_sapply <- sapply(
  fuentes_cuan_sel,
  function(archivo) {
    r_i <- raster(archivo)
    raster::extract(x = r_i, y = puntos)
  }) %>%
  as.data.frame %>% 
  setNames(gsub('.tif', '', basename(fuentes_cuan_sel)))
```

- Con bucle `for`, deberías obtener el mismo resultado. Usar bucle `for`
  es más compatible con los bloques de control de flujo de otros
  lenguajes de programación:

``` r
# Inicializa una lista vacía para almacenar los data.frames
df_resultados <- list()
# Itera sobre los elementos de `fuentes_cuan_sel`
for (archivo in fuentes_cuan_sel) {
  # Carga el ráster
  r_a <- raster(archivo)
  # Extrae los valores y los convierte en un data.frame
  v <- raster::extract(x = r_a, y = puntos)
  # Agrega el data.frame a la lista
  df_resultados[[archivo]] <- v
  }
df_valores_cuan_sel_for <- df_resultados %>%
  as.data.frame %>%
  setNames(gsub('.tif', '', basename(fuentes_cuan_sel)))
```

Comprueba que tus resultados con la función `sapply` y con `for`, son
idénticos.

``` r
all.equal(df_valores_cuan_sel_sapply, df_valores_cuan_sel_for)
```

Los valores extraídos desde los cinco rásters asignados al tali para sus
cinco puntos asignados, se verían así (si los tuyos coinciden con estos,
algo anda mal):

| CGIAR-ELEVACION | G90-PENDIENTE | CHELSA_bio15_1981-2010_V21 | CHELSA_bio1_1981-2010_V21 | CHELSA_bio10_1981-2010_V21 |
|:---------------:|:-------------:|:--------------------------:|:-------------------------:|:--------------------------:|
|     1948.86     |     7.09      |            469             |           2882            |            2894            |
|      42.51      |     0.99      |            371             |           2992            |            3005            |
|      89.28      |     2.83      |            385             |           2991            |            3004            |
|      19.13      |     0.65      |            433             |           2989            |            3001            |
|     654.14      |     5.14      |            495             |           2958            |            2971            |
|     231.23      |     5.19      |            461             |           2981            |            2992            |
|     622.91      |     2.14      |            270             |           2951            |            2967            |
|     238.35      |     3.05      |            273             |           2978            |            2992            |
|     287.03      |     3.01      |            275             |           2976            |            2994            |
|     427.51      |     0.62      |            524             |           2975            |            2986            |

Ahora es necesario convertir los valores de las variables del conjunto
CHELSA, dado que usan factor de escala y, algunas también tienen
*offset*. Aplicando este código, podrás lograrlo

``` r
vars_offset <- paste0('_bio', c(1,5:6, 8:11), '_')
escala <- 0.1
offset <- -273.15
df_valores_cuan_sel_reales <- df_valores_cuan_sel_sapply %>%
  mutate(
    # Aplica la escala de 0.1 a todas las columnas que contienen "bio" y un número
    across(matches("bio\\d+"), ~ . * 0.1),
    
    # Aplica el offset de -273.15 solo a las columnas cuyo nombre contiene alguna de las cadenas en `vars_offset`
    across(matches(paste(vars_offset, collapse = "|")), ~ . - 273.15))
```

Los valores extraídos desde los cinco rásters asignados al tali para sus
cinco puntos asignados, luego de aplicar el escalado y el *offset*, se
verían así:

| CGIAR-ELEVACION | G90-PENDIENTE | CHELSA_bio15_1981-2010_V21 | CHELSA_bio1_1981-2010_V21 | CHELSA_bio10_1981-2010_V21 |
|:---------------:|:-------------:|:--------------------------:|:-------------------------:|:--------------------------:|
|     1948.86     |     7.09      |            46.9            |           15.05           |           16.25            |
|      42.51      |     0.99      |            37.1            |           26.05           |           27.35            |
|      89.28      |     2.83      |            38.5            |           25.95           |           27.25            |
|      19.13      |     0.65      |            43.3            |           25.75           |           26.95            |
|     654.14      |     5.14      |            49.5            |           22.65           |           23.95            |
|     231.23      |     5.19      |            46.1            |           24.95           |           26.05            |
|     622.91      |     2.14      |            27.0            |           21.95           |           23.55            |
|     238.35      |     3.05      |            27.3            |           24.65           |           26.05            |
|     287.03      |     3.01      |            27.5            |           24.45           |           26.25            |
|     427.51      |     0.62      |            52.4            |           24.35           |           25.45            |

> Transcribe tu tabla a papel.

> No olvides tu párrafo al final del ejercicio.

# Ejercicio 3. Construye una matriz de correlación y calcula el coeficiente de correlación

Construye una matriz de correlación usando el código que se muestra
abajo. Redondea el coeficiente (o índice) de correlación de Pearson a
tres cifras significativas para simplificar.

``` r
# Calcula la matriz de correlación
correlacion <- cor(df_valores_cuan_sel_reales)

# Crea una matriz de NA con las mismas dimensiones que la matriz de correlación
correlacion_inferior <- correlacion

# Establece los valores del triángulo superior a NA
correlacion_inferior[upper.tri(correlacion_inferior, diag = T)] <- NA
correlacion_inferior <- round(correlacion_inferior, 3)
correlacion_inferior[is.na(correlacion_inferior)] <- ""
```

Así se vería la matriz de correlación del tali:

``` r
# Muestra la matriz con solo el triángulo inferior
correlacion_inferior %>% knitr::kable()
```

|                            | CGIAR-ELEVACION | G90-PENDIENTE | CHELSA_bio15_1981-2010_V21 | CHELSA_bio1_1981-2010_V21 | CHELSA_bio10_1981-2010_V21 |
|:---------------------------|:----------------|:--------------|:---------------------------|:--------------------------|:---------------------------|
| CGIAR-ELEVACION            |                 |               |                            |                           |                            |
| G90-PENDIENTE              | 0.71            |               |                            |                           |                            |
| CHELSA_bio15_1981-2010_V21 | 0.273           | 0.221         |                            |                           |                            |
| CHELSA_bio1_1981-2010_V21  | -0.994          | -0.7          | -0.199                     |                           |                            |
| CHELSA_bio10_1981-2010_V21 | -0.996          | -0.703        | -0.254                     | 0.998                     |                            |

> Transcribe tu matriz de correlación a papel.

Ahora reproduce, con cálculos manuales (en papel, papelito, papelito,
“♪…me tumba con papelito…♪”), el valor del coeficiente de correlación de
Pearson obtenido para el par de variables que obtuvo mayor valor
absoluto. Por ejemplo, el valor absoluto de un coeficiente de
correlación -0.98 es mayor que +0.92.

En el caso del tali, esta parte del ejercicio se haría con las variables
`CHELSA_bio1_1981-2010_V21` y `CGIAR-ELEVACION`. Estos serían los
vectores:

1.  **CHELSA_bio1_1981-2010_V21**:
    - 15.05, 26.05, 25.95, 25.75, 22.65, 24.95, 21.95, 24.65, 24.45,
      24.35
2.  **CGIAR-ELEVACION**:
    - 1948.86, 42.51, 89.28, 19.13, 654.14, 231.23, 622.91, 238.35,
      287.03, 427.51

A continuación incluyo una demostración de cómo calcular el coeficiente
de correlación de Pearson son esos vectores.

## Fórmula del Coeficiente de Correlación de Pearson

La fórmula para el coeficiente de correlación de Pearson ($r$) es:

$$
r = \frac{\sum (X_i - \overline{X})(Y_i - \overline{Y})}{\sqrt{\sum (X_i - \overline{X})^2 \sum (Y_i - \overline{Y})^2}}
$$

donde:

- $X_i$ y $Y_i$ son los valores individuales de los vectores.
- $\overline{X}$ y $\overline{Y}$ son las medias de los vectores $X$ y
  $Y$, respectivamente.

## Pasos de Cálculo

1.  **Calcular las medias de los vectores** ($\overline{X}$ y
    $\overline{Y}$):

    $$
    \overline{X} = \frac{15.05 + 26.05 + \ldots + 24.35}{10}
    $$

    $$
    \overline{X} = 23.28
    $$

    $$
    \overline{Y} = \frac{1948.86 + 42.51 + \ldots + 427.51}{10}
    $$

    $$
    \overline{Y} = 456.89
    $$

2.  **Calcular las desviaciones de cada valor respecto a sus medias**:

    Ejemplo para el primer valor:

    $$
    (X_1 - \overline{X}) = 15.05 - 23.28 = -8.23
    $$

    $$
    (Y_1 - \overline{Y}) = 1948.86 - 456.89 = 1491.97
    $$

    Repite este cálculo para cada par de valores.

3.  **Calcular los productos de las desviaciones**
    $(X_i - \overline{X})(Y_i - \overline{Y})$:

    Para el primer valor:

    $$
    (-8.23) \times (1491.97) = -12272.89
    $$

    Repite este cálculo para todos los pares y luego suma los
    resultados.

4.  **Calcular los cuadrados de las desviaciones y sumarlos**:

    Para $X$:

    $$
    (X_1 - \overline{X})^2 = (-8.23)^2 = 67.74
    $$

    Para $Y$:

    $$
    (Y_1 - \overline{Y})^2 = (1491.97)^2 = 2225812.68
    $$

    Repite y suma los valores para ambos vectores.

5.  **Retomando la fórmula del coeficiente de correlación de Pearson**:

    $$
    r = \frac{\sum (X_i - \overline{X})(Y_i - \overline{Y})}{\sqrt{\sum (X_i - \overline{X})^2 \sum (Y_i - \overline{Y})^2}}
    $$

6.  Tenemos los siguientes valores calculados y sustituimos en la
    fórmula.

- **Media de X ($\overline{X}$)**: $23.58$
- **Media de Y ($\overline{Y}$)**: $456.09$
- **Suma de los productos de las desviaciones**:
  $\sum (X_i - \overline{X})(Y_i - \overline{Y}) = -16738.65$
- **Suma de los cuadrados de las desviaciones de X**:
  $\sum (X_i - \overline{X})^2 = 97.08$
- **Suma de los cuadrados de las desviaciones de Y**:
  $\sum (Y_i - \overline{Y})^2 = 2919317.85$

$$
r = \frac{-16738.65}{\sqrt{97.08 \times 2919317.85}}
$$

$$
r = \frac{-16738.65}{\sqrt{283445690.61}}
$$

$$
r = \frac{-16738.65}{16832.92}
$$

$$
r = -0.994
$$

El coeficiente de correlación de Pearson ($r$) es aproximadamente
**-0.994**, lo que indica una relación lineal negativa muy fuerte entre
los dos conjuntos de datos. El valor es parecido al obtenido por la
función `cor`, -0.996.

> No olvides tu párrafo al final del ejercicio.

# Ejercicio 4. Representa tus variables

Representa tu par de variables con mayor valor absoluto del coeficiente
de correlación de Pearson en un gráfico de dispersión dibujado a mano.

El ejercicio, aplicado al caso del tali, se muestra a continuación.

Puedes usar los bloques de código de abajo, sustituyendo por tus
variables, para asistirte en el dibujo. Los nombres las columnas en el
`data.frame` los podrás elegir desde el desplegable que aparece al
presionar la tecla `TAB` luego de escribir el nombre del `data.frame` en
la consola de R)

``` r
var1 <- df_valores_cuan_sel_reales$`CGIAR-ELEVACION` #<-----Atender aquí
var2 <- df_valores_cuan_sel_reales$`CHELSA_bio1_1981-2010_V21` #<-----Atender aquí
```

En el caso del tali, el gráfico de dispersión sería este.

``` r
plot(var1, var2)
abline(lm(var2 ~ var1), col = 'red')
```

<img src="README_files/figure-gfm/unnamed-chunk-25-1.png" width="100%" />

> Dibuja tu gráfico en papel.

> No olvides tu párrafo al final del ejercicio.

# Ejercicio 5. Çonstruye un modelo por medio de regresión lineal simple

Usando el par de variables con el mayor valor absoluto del coeficiente
de correlación de Pearson, construye un modelo de regresión lineal
simple asistiéndote por R para los cálculos. Elige una variable
respuesta o dependiente (lado izquierdo de la ecuación) y una variable
independiente o predictora (lado derecho de la ecuación).

Haré una demostración de cómo se generaría un modelo de regresión lineal
simple a mano con el par de variables que mayor valor absoluto obtuvo en
el coeficiente de correlación de Pearson, que fueron CGIAR-ELEVACION
(elevación) y CHELSA_bio1_1981-2010_V21 (temperatura media anual).

Usaremos los valores de elevación como $x$ (variable independiente) y
los valores de temperatura como $y$ (variable dependiente), pero en tu
caso debes elegir los que correspondan. Sigamos los pasos para estimar
los coeficientes de la regresión lineal simple, $\beta_0$ y $\beta_1$.

1.  Recopilación de los datos

``` r
# Creación de los vectores en R
x <- df_valores_cuan_sel_reales$`CGIAR-ELEVACION` #<-----Atender aquí
# 1948.86267   42.51299   89.28226   19.12749  654.14288  231.23085  622.91357  238.35159  287.03076  427.51349
y <- df_valores_cuan_sel_reales$`CHELSA_bio1_1981-2010_V21` #<-----Atender aquí
# 15.05 26.05 25.95 25.75 22.65 24.95 21.95 24.65 24.45 24.35
```

2.  Cálculo de las medias de $x$ y $y$

Las fórmulas son:

$$
\bar{x} = \frac{\sum x_i}{n}
$$

$$
\bar{y} = \frac{\sum y_i}{n}
$$

El código en R es:

``` r
mean_x <- mean(x)
mean_y <- mean(y)
format(mean_x, scientific = F)
```

    ## [1] "456.0969"

``` r
format(mean_y, scientific = F)
```

    ## [1] "23.58"

Resultado:

- $\bar{x} = 456.0968567$
- $\bar{y} = 23.58$

3.  Cálculo de los valores necesarios para $\beta_1$ y $\beta_0$

Fórmulas para las sumas necesarias:

$$
\sum x_i y_i = \sum (x_i \times y_i)
$$

$$
\sum x_i^2 = \sum (x_i^2)
$$

$$
\sum x_i = \sum x_i
$$

$$
\sum y_i = \sum y_i
$$

El código en R es:

``` r
sum_xy <- sum(x * y)
sum_x_squared <- sum(x^2)
sum_x <- sum(x)
sum_y <- sum(y)

format(sum_xy, scientific = F)
```

    ## [1] "90808.98"

``` r
format(sum_x_squared, scientific = F)
```

    ## [1] "4999568"

``` r
format(sum_x, scientific = F)
```

    ## [1] "4560.969"

``` r
format(sum_y, scientific = F)
```

    ## [1] "235.8"

Resultado:

- $\sum x_i y_i = 90808.98$
- $\sum x_i^2 = 4999568$
- $\sum x_i = 4560.969$
- $\sum y_i = 235.8$

4.  Cálculo de $\beta_1$ (pendiente)

La fórmula para calcular $\beta_1$ es:

$$
\beta_1 = \frac{\sum (x_i y_i) - \frac{\sum x_i \sum y_i}{n}}{\sum (x_i^2) - \frac{(\sum x_i)^2}{n}}
$$

El código en R es:

``` r
n <- length(x)
beta_1 <- (sum_xy - (sum_x * sum_y) / n) / (sum_x_squared - (sum_x^2) / n)
format(beta_1, scientific = F)
```

    ## [1] "-0.005733745"

Resultado:

- $\beta_1 = -0.005733745$

5.  Cálculo de $\beta_0$ (intercepto)

La fórmula para calcular $\beta_0$ es:

$$
\beta_0 = \bar{y} - \beta_1 \bar{x}
$$

El código en R es:

``` r
beta_0 <- mean_y - beta_1 * mean_x
format(beta_0, scientific = F)
```

    ## [1] "26.19514"

Resultado:

- $\beta_0 = 26.19514$

6.  Formulación de la ecuación de la recta de regresión

La ecuación de la recta de regresión es:

$$
\hat{y} = \beta_0 + \beta_1 x
$$

Sustituyendo los valores calculados:

$$
\hat{y} = 26.19514 -0.005733745x
$$

## Resumen

La recta de regresión ajustada para estos datos es:

$$
\hat{y} = 26.1951431 -0.0057337x
$$

> No olvides tu párrafo al final del ejercicio.

<div id="refs" class="references csl-bib-body hanging-indent"
entry-spacing="0">

<div id="ref-jose_ramon_martinez_batlle_2022_7367180" class="csl-entry">

Martínez-Batlle, José Ramón. 2022.
“<span class="nocase">geofis/zonal-statistics: Let there be
environmental variables</span>.” Zenodo.
<https://doi.org/10.5281/zenodo.7367256>.

</div>

</div>
