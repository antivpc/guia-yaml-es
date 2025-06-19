[:point_up_2: Volver al Índice](README.md) | [:point_left: Capítulo 2](capitulo-2.md) | [:point_right: Capítulo 4](capitulo-4.md)

---

# Capítulo 3 - Tipos de Datos y Representación

## 1. Cadenas de Texto (Strings)

Las **cadenas de texto** o **strings** son el tipo de dato más común en YAML y se utilizan para representar texto. YAML es muy flexible en cómo se definen las cadenas, lo que permite una gran legibilidad en la mayoría de los casos.

### Cadenas sin Comillas

La forma más sencilla y común de definir una cadena de texto en YAML es **sin usar comillas**. YAML es lo suficientemente inteligente como para inferir que un valor es una cadena de texto si no parece ser otro tipo de dato (número, booleano, nulo, etc.).

* **Cuándo usarlas:** Para la mayoría de los textos simples que no contienen caracteres especiales de YAML o no son ambiguos con otros tipos de datos.
* **Ejemplo:**

    ```yaml
    mensaje: Hola mundo
    nombre_usuario: alice123
    version: 1.0.0 # Aunque tenga números y puntos, YAML lo interpreta como cadena aquí
    ```

* **Consideraciones:**
    * No deben comenzar con los caracteres `-`, `?`, `:`, `[`, `{`, `#`, `&`, `*`, `!`, `|`, `>`, `'`, `"`, `%`, `@`, `` ` `` (espacio al inicio sí está permitido después de la indentación).
    * No deben ser palabras que YAML interprete como booleanos (`true`, `false`, `yes`, `no`, `on`, `off`) o nulos (`null`, `~`).
    * Si la cadena contiene `: ` (dos puntos seguidos de un espacio), el analizador YAML podría interpretarla como una nueva clave, lo que causaría un error o una interpretación incorrecta.

### Cadenas con Comillas Simples y Dobles

Cuando una cadena de texto contiene caracteres especiales, necesita ser **encerrada entre comillas**. YAML soporta tanto comillas simples (`'`) como comillas dobles (`"`), cada una con sus propias características.

**a) Cadenas con Comillas Dobles (`""`)**

* **Uso:** Las comillas dobles permiten el uso de **secuencias de escape** (como `\n` para un salto de línea, `\t` para una tabulación, o `\"` para incluir comillas dobles dentro de la cadena).
* **Cuándo usarlas:** Ideales cuando necesitas incluir caracteres especiales que de otra manera serían problemáticos, o cuando necesitas saltos de línea explícitos dentro de la cadena.
* **Ejemplo:**

    ```yaml
    saludo_escape: "Hola a todos.\nEsto es un salto de línea."
    ruta_windows: "C:\\Users\\Usuario\\Documentos" # Se necesita \\ para escapar \
    frase_citada: "Ella dijo: \"Hola.\""
    ```

**b) Cadenas con Comillas Simples (`''`)**

* **Uso:** Las comillas simples son útiles cuando quieres que el contenido de la cadena se interprete **literalmente**, sin procesar secuencias de escape. La única excepción es para incluir una comilla simple dentro de la cadena, donde se escapa duplicándola (`''`).
* **Cuándo usarlas:** Cuando tu texto contiene caracteres que podrían ser interpretados como secuencias de escape (`\`) o cuando deseas asegurarte de que el valor sea tratado como una cadena, incluso si parece un número, booleano, etc.
* **Ejemplo:**

    ```yaml
    literal_barra: 'C:\data\file.txt' # La barra inversa se mantiene tal cual
    mensaje_literal: 'Esto es un mensaje literal, \n no se escapa la barra n.'
    frase_con_apostrofe: 'No puedo ir' # Para incluir una comilla simple: 'No puedo''t ir'
    numero_como_texto: '12345' # Forzar que sea una cadena, no un número
    ```

### Cadenas Multilínea (Literales y Plegadas)

Para textos largos que abarcan múltiples líneas, YAML ofrece dos estilos de bloque que mejoran drásticamente la legibilidad: el **estilo literal** (`|`) y el **estilo plegado** (`>`).

**a) Estilo Literal (`|`)**

* **Uso:** Conserva **todos los saltos de línea** y los espacios de indentación iniciales (excepto la indentación del bloque YAML en sí) exactamente como están escritos.
* **Cuándo usarlo:** Cuando el formato exacto del texto es importante, como en el caso de bloques de código, poesía o mensajes donde cada salto de línea es relevante.
* **Ejemplo:**

    ```yaml
    poema: |
      Un día la tierra
      tembló y la luna
      se hizo pedazos.
    ```
    Cuando este YAML se procesa, la cadena resultante sería: `"Un día la tierra\ntembló y la luna\nse hizo pedazos.\n"` (nota el salto de línea final, que se incluye por defecto).

    Puedes controlar el salto de línea final añadiendo un indicador:
    * `|+`: Conserva el salto de línea final explícitamente.
    * `|-`: Elimina el salto de línea final.
    * `|`: Comportamiento por defecto (generalmente mantiene un salto de línea final si lo hay, pero puede variar ligeramente entre procesadores).

    ```yaml
    mensaje_final: |-
      Gracias por usar
      nuestro servicio.
    ```
    Esto resultaría en `"Gracias por usar\nnuestro servicio."` (sin el salto de línea al final de la cadena).

**b) Estilo Plegado (`>`)**

* **Uso:** Convierte los saltos de línea en **espacios individuales**, "plegando" el texto en una sola línea lógica. Los saltos de línea en blanco (líneas vacías) se conservan como saltos de párrafo.
* **Cuándo usarlo:** Para textos largos donde el flujo del párrafo es más importante que la conservación exacta de los saltos de línea, como descripciones o párrafos de documentación.
* **Ejemplo:**

    ```yaml
    descripcion_producto: >
      Este producto es innovador y ofrece
      soluciones avanzadas para la gestión de datos.
      Está diseñado para optimizar el rendimiento
      y la escalabilidad en entornos complejos.
    ```
    Cuando este YAML se procesa, la cadena resultante sería: `"Este producto es innovador y ofrece soluciones avanzadas para la gestión de datos. Está diseñado para optimizar el rendimiento y la escalabilidad en entornos complejos.\n"`

    Al igual que con el estilo literal, puedes controlar el salto de línea final con `>+` o `>-`.

    ```yaml
    parrafo_largo: >-
      Primer párrafo.

      Segundo párrafo.
    ```
    Esto resultaría en `"Primer párrafo.\nSegundo párrafo."` (el salto de línea entre párrafos se conserva, pero los saltos de línea simples se convierten en espacios, y no hay salto de línea final).

Entender estas diferentes formas de manejar cadenas es crucial para escribir archivos YAML claros y para evitar sorpresas al parsear tus datos.

---

## 2. Números

Los **números** son otro tipo de dato fundamental en YAML, utilizados para representar valores cuantitativos. YAML es bastante flexible y puede inferir automáticamente si un valor es un entero o un flotante. No necesitas usar comillas para la mayoría de los números, a menos que quieras forzarlos a ser tratados como cadenas de texto.

### Enteros

Los **enteros** son números sin componentes fraccionarios. YAML puede reconocer enteros en varias bases:

* **Decimal:** La forma más común, números que usamos a diario.
    ```yaml
    edad: 30
    cantidad_productos: 150
    puntos: -50
    ```

* **Octal:** Números que comienzan con `0o` (cero seguido de la letra 'o').
    ```yaml
    permisos_octal: 0o755 # Representa 493 en decimal
    ```

* **Hexadecimal:** Números que comienzan con `0x`.
    ```yaml
    color_hex: 0xFF # Representa 255 en decimal
    error_code: 0xA4 # Representa 164 en decimal
    ```

* **Binario:** Números que comienzan con `0b`.
    ```yaml
    banderas_binarias: 0b101101 # Representa 45 en decimal
    ```

### Flotantes

Los **flotantes** (o números de coma flotante) son números que incluyen una parte fraccionaria, indicada por un punto (`.`).

* **Sintaxis básica:**
    ```yaml
    temperatura: 25.5
    precio_unitario: 19.99
    gravedad: 9.81
    ```

* **Valores especiales:** YAML también soporta representaciones para infinito y no-un-número.
    * **Infinito:** `inf`, `Inf`, `INF`, `+inf`, `+Inf`, `+INF` para infinito positivo. `-inf`, `-Inf`, `-INF` para infinito negativo.
        ```yaml
        limite_superior: .inf # Equivalente a infinito positivo
        limite_inferior: -.inf # Equivalente a infinito negativo
        ```
    * **No es un número (NaN):** `.nan`, `.Nan`, `.NAN`. Se usa para representar un valor indefinido o no representable numéricamente (por ejemplo, el resultado de una división por cero).
        ```yaml
        resultado_indefinido: .nan
        ```

### Notación Exponencial

Para números muy grandes o muy pequeños, puedes usar la **notación exponencial** (también conocida como notación científica o notación E), donde un número es seguido por `e` o `E` y un exponente.

* **Sintaxis:** `[número][e|E][+/-][exponente]`
* **Ejemplo:**
    ```yaml
    distancia_luz: 3.0e+8 # 3.0 * 10^8 (300,000,000)
    carga_electron: -1.602176634e-19 # -1.602176634 * 10^-19
    ```

**Consideraciones clave para números en YAML:**

* **Comillas:** Si un número contiene caracteres que no son dígitos (excepto el punto decimal o los prefijos de base) o si quieres que un número sea tratado estrictamente como una cadena de texto (ej. un ID que parece número pero tiene ceros iniciales que deben preservarse), debes **encerrarlo entre comillas**.
    ```yaml
    id_producto: "007" # Esto será tratado como la cadena "007", no el número 7
    numero_de_telefono: "555-1234" # Esto es una cadena debido al guion
    ```
* **Separadores de miles:** YAML **no soporta** separadores de miles (como comas o puntos, dependiendo de la configuración regional) dentro de los números. Si los usas, el valor se interpretará como una cadena.
    ```yaml
    valor_incorrecto: 1,000,000 # Esto será la cadena "1,000,000"
    valor_correcto: 1000000 # Esto será el número 1000000
    ```

Comprender cómo YAML maneja los diferentes tipos de números te permitirá representar datos cuantitativos de forma precisa y evitar interpretaciones erróneas de tus configuraciones.

---

## 3. Booleanos

Los **booleanos** son un tipo de dato lógico que puede tener solo uno de dos valores: **verdadero** o **falso**. Se utilizan comúnmente para configurar interruptores (on/off), banderas (flags) o condiciones binarias en archivos de configuración.

YAML es muy flexible y amigable con el usuario en cuanto a la representación de valores booleanos. Reconoce un conjunto de palabras que, sin comillas, son automáticamente interpretadas como `true` o `false`. Esta flexibilidad mejora la legibilidad para diferentes audiencias.

### Valores Verdaderos y Falsos

YAML es "case-insensitive" para la mayoría de las representaciones booleanas, lo que significa que no distingue entre mayúsculas y minúsculas para estas palabras clave.

**Representaciones de `true` (verdadero):**

* `true`
* `True`
* `TRUE`
* `on`
* `On`
* `ON`
* `yes`
* `Yes`
* `YES`

**Representaciones de `false` (falso):**

* `false`
* `False`
* `FALSE`
* `off`
* `Off`
* `OFF`
* `no`
* `No`
* `NO`

**Ejemplos de uso:**

```yaml
# Ejemplos de valores verdaderos
habilitar_notificaciones: true
activar_cache: ON
modo_debug: yes

# Ejemplos de valores falsos
deshabilitar_envio_email: false
permitir_publico: No
uso_experimental: off

# Un booleano dentro de una lista
opciones:
  - nombre: guardar_log
    valor: True
  - nombre: procesar_datos
    valor: False
```

**Consideraciones importantes:**

* **Sin comillas:** Para que YAML interprete el valor como un booleano, **no debe estar entre comillas**. Si pones ` "true" ` o ` 'false' `, YAML lo tratará como una cadena de texto, no como un valor booleano. Esto es crucial si tu sistema espera un tipo de dato booleano y no una cadena.
    ```yaml
    # Esto es un booleano (correcto)
    estado_activo: true

    # Esto es una cadena de texto (¡no es un booleano!)
    cadena_booleana: "true"
    ```
* **Contexto:** Aunque YAML es inteligente, siempre considera el contexto. Si una palabra como "No" está claramente en un contexto donde se espera una cadena (por ejemplo, `nombre: Noé`), la mayoría de los analizadores modernos no la confundirán con un booleano `false`. Sin embargo, para evitar cualquier ambigüedad, si una cadena de texto coincide con una palabra booleana y el contexto no es obvio, es una buena práctica encerrarla entre comillas.
    ```yaml
    # Aquí "No" es una cadena porque se espera un nombre
    primer_nombre: No

    # Aquí "no" es un booleano porque el contexto implica una condición
    permitir_acceso: no
    ```

El uso adecuado de los booleanos facilita la configuración lógica de tus aplicaciones y sistemas, permitiendo una fácil activación o desactivación de funcionalidades con solo un vistazo al archivo YAML.

---

## 4. Nulos

En YAML, un valor **nulo** (o `null`) se utiliza para representar la ausencia intencional de datos o un valor indefinido. Es análogo a `null` en JavaScript, `None` en Python, o `nil` en Ruby. Indicar que un valor es nulo es diferente a simplemente dejar una clave vacía, ya que un valor nulo comunica explícitamente que no hay datos para esa entrada.

### Representación de Valores Nulos

YAML ofrece dos formas principales para representar un valor nulo, y ambas son igualmente válidas:

1.  **`null`:** La palabra `null` (insensible a mayúsculas y minúsculas: `null`, `Null`, `NULL`) es la forma más común y explícita de indicar un valor nulo.

2.  **`~` (tilde):** El carácter `~` también se reconoce como una representación de un valor nulo. Es una opción más concisa.

**Ejemplos de uso:**

```yaml
# Ejemplos usando 'null'
descripcion_opcional: null
valor_por_defecto: NULL
configuracion_no_establecida: Null

# Ejemplos usando '~'
sin_contenido: ~
datos_vacios: ~

# Un campo opcional en una lista de objetos
producto:
  id: P101
  nombre: Laptop Ultraligera
  descripcion: null # Este producto no tiene una descripción detallada

usuario:
  id: 205
  username: jdoe
  email: jdoe@example.com
  telefono: ~ # No se proporcionó un número de teléfono
```

**Consideraciones importantes:**

* **Ausencia de valor vs. Cadena vacía:** Es importante distinguir entre un valor nulo y una cadena de texto vacía.
    * `clave: null` o `clave: ~` indica que el valor es nulo.
    * `clave: ""` o `clave: ''` indica que el valor es una cadena de texto vacía (un valor existente, solo que sin caracteres).
    * Dejar una clave sin valor (ej. `clave:`) es a menudo interpretado como un valor nulo por los analizadores YAML, pero es una buena práctica usar `null` o `~` para mayor claridad.

    ```yaml
    # Esto es un valor nulo
    campo_nulo: null

    # Esto es una cadena vacía
    campo_vacio_string: ""

    # Esto generalmente se interpreta como nulo (pero menos explícito)
    campo_implicito_nulo:
    ```

* **Sin comillas:** Al igual que con los booleanos y los números, para que `null` o `~` sean interpretados como un valor nulo, **no deben estar entre comillas**. Si los pones entre comillas (ej. `"null"`), serán tratados como una cadena de texto literal.

Los valores nulos son particularmente útiles en configuraciones donde ciertos parámetros son opcionales y su ausencia necesita ser explícitamente declarada, o cuando un valor no se ha determinado aún.

---

## 5. Fechas y Horas

YAML es capaz de reconocer y parsear automáticamente diferentes formatos de fechas y horas, convirtiéndolos en el tipo de dato de fecha/hora nativo del lenguaje de programación que esté procesando el YAML (por ejemplo, objetos `datetime` en Python, `Date` en JavaScript). Esto elimina la necesidad de que los desarrolladores realicen el parseo de cadenas manualmente, siempre y cuando se sigan los formatos estándar.

### Formatos Estándar de Fecha y Hora

YAML se adhiere a los estándares ISO 8601 para la representación de fechas y horas, lo que garantiza la interoperabilidad y la consistencia.

Aquí están los formatos más comunes que YAML puede reconocer implícitamente:

* **Fecha (Date):** `AAAA-MM-DD` (año-mes-día)
    ```yaml
    fecha_nacimiento: 1990-07-20
    fecha_evento: 2025-12-25
    ```

* **Hora (Time):** `HH:MM:SS` o `HH:MM:SS.fracción` (horas:minutos:segundos con o sin fracción de segundos)
    ```yaml
    hora_apertura: 09:00:00
    hora_cierre: 17:30:15.500
    ```

* **Fecha y Hora (Timestamp):** `AAAA-MM-DD HH:MM:SS` o `AAAA-MM-DD HH:MM:SS.fracción`
    ```yaml
    inicio_sesion: 2024-06-19 10:30:00
    ultima_actualizacion: 2024-06-19 10:51:58.123
    ```

* **Fecha y Hora con Zona Horaria (Timestamp with Timezone):** `AAAA-MM-DD HH:MM:SS ZZZ` (donde `ZZZ` es el offset de la zona horaria)
    ```yaml
    hora_servidor_utc: 2024-06-19 14:00:00Z # Hora UTC (Z para Zulu/UTC)
    hora_local_lapaz: 2024-06-19 10:51:58-04:00 # Con offset de -4 horas de UTC
    hora_local_berlin: 2024-06-19 16:51:58+02:00 # Con offset de +2 horas de UTC
    ```

    **Nota sobre la Zona Horaria:** El offset de la zona horaria se especifica como `+/-HH:MM` o simplemente `Z` para UTC. Algunos procesadores de YAML también pueden entender nombres de zonas horarias (ej. `America/La_Paz`), pero esto es menos común como una inferencia automática y podría requerir que el analizador tenga soporte específico. El formato ISO 8601 con offset numérico es el más seguro.

**Ejemplos Combinados:**

```yaml
eventos:
  - nombre: Conferencia Tech
    fecha_inicio: 2024-09-15
    hora_registro: 08:30:00
    # Fecha y hora exacta con zona horaria (útil para eventos globales)
    apertura_puertas: 2024-09-15 08:00:00-05:00

  - nombre: Lanzamiento Producto
    # Solo fecha y hora sin segundos o zona horaria
    fecha_hora_lanzamiento: 2025-01-20 14:00

  - nombre: Revisión Semanal
    # Solo fecha
    proxima_revision: 2024-06-26
```

**Consideraciones importantes:**

* **Sin comillas:** Al igual que con los números y booleanos, para que YAML infiera el tipo de dato de fecha/hora, el valor **no debe estar entre comillas**. Si pones ` "2024-06-19" `, será tratado como una cadena de texto.
* **Precisión:** La precisión de la fracción de segundos puede variar entre implementaciones de YAML y los lenguajes de programación. Si la precisión es crítica, asegúrate de que tu procesador YAML y el tipo de dato de destino la soporten.
* **Ambiguidad:** Aunque YAML es inteligente, si una cadena es ambiguamente una fecha/hora y otra cosa (ej. `2024-06-19` podría ser una cadena representando una versión de software), el contexto o la especificación explícita de un tipo pueden ser necesarios en casos muy raros, pero para los formatos ISO 8601, la inferencia suele ser robusta.

Utilizar los formatos estándar de fecha y hora en YAML asegura que tus datos temporales sean interpretados correctamente por diferentes sistemas y aplicaciones, facilitando la gestión de eventos, logs y programaciones.

---

¡Genial! Pasemos a la última sección del Capítulo 3, la **3.6 Binario**. Esta es una sección opcional porque el manejo directo de datos binarios en YAML no es tan común como los otros tipos, pero es importante saber que la capacidad existe.

---

## 6. Binario (opcional, para casos específicos)

YAML, en su especificación completa, tiene la capacidad de incluir datos binarios directamente dentro de un documento. Sin embargo, este es un caso de uso mucho menos común que las cadenas de texto, números, booleanos o fechas. Generalmente, cuando se necesitan incluir datos binarios (como imágenes, archivos de audio o documentos compilados), es más habitual almacenarlos externamente y referenciarlos mediante una ruta o URL en el archivo YAML.

No obstante, si la necesidad surge, YAML permite codificar datos binarios utilizando la codificación **Base64**. Base64 es un método para codificar datos binarios en un formato de texto ASCII, lo que permite su inclusión en documentos basados en texto como YAML.

* **¿Por qué Base64?** Los datos binarios raw (crudos) no son caracteres de texto estándar y podrían corromper o ser malinterpretados por un analizador de texto. Base64 asegura que los datos binarios se representen de forma segura dentro de un archivo de texto.

* **Sintaxis:** Para indicar que un valor es un dato binario codificado en Base64, se utiliza una **etiqueta explícita de tipo** `!!binary`. La etiqueta se coloca antes del valor y le indica al analizador YAML cómo interpretar el contenido.

```yaml
clave: !!binary <datos_codificados_en_base64>
```

**Ejemplo de uso:**

Imagina que tienes una pequeña imagen de un icono que quieres incrustar directamente en tu archivo de configuración YAML, o quizás un certificado.

```yaml
configuracion_app:
  nombre: MiAppSegura
  icono_pequeno: !!binary |
    iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAMAAAAoLQ9TAAAABGdBTUEAALGPC/
    xhBQAAACBjSFJNAAB6JQAAgIQAAPoAAACA6AAAdTAAAOpgAAC6BLpG/wAAADN
    QTFRFAAAAAP//////////////////////////////////////////////////
    ////////////AAAAAEe27wAAAAF0Uk5TAAEAAAAo4L+yAAAAAWJLR0QCgA
    j0/QAAAACXBIWXMAAAsTAAALEwEAmpwYAAAAB3RJTUUH4ggGEC073r77AAAAA
    BJRU5ErkJggg==
  firma_digital: !!binary |
    LS0tLUJFR0lOIENFUlRJRklDQVRFLS0tLQpNSUlEWURDQWdRQmFEZ0xNQTBHQT
    FVRUF3d0xVbXhzWkdWc2JpQlVhMjVsY3k1amIyMHRYM0poYlhCdQpnQTFVRUF3
    d0xVbXhzWkdWc2JpQlVhMjVsY3k1amIyMHRYM0poYlhCdQpYekFOQmdOVkJBZ1
    RBMkZuWVdOMFFYSmhiakNCYmpBZk5qQTVNelV3TVRFd01UTTVlVEFOCkJnTnZC
    QWNUQjJadVltOXNaQ0JzWVhOelpYTnpYMlpoYlhOMGJ5NXBibXhvZVEwMU1EQX
    dOREF3TnpZMApNekEyTnpNMU1UVTJZamcwTVRCQ0FnRUFBMGhkQ0JNU0NVR0FU
    QkVsQUlBSHh4U2xHNGh6b2xQbzB2TGNpSwpnZ0FNQWdFQUx0dC91N0h3a0VjQW
    dkWkhBQmdTQUFBQ0FnRUEwbk5wcm96R21iUm9QdmV3Clh6QlFCZ0dkcmRzQUlG
    SlVvMkVndEdzT0RBbFl1a0VBaWJkVEFhMThLd2g5eWJ6NlJ0eTYKY2dGNm96Qk
    VBdjdWZXl0RUtxTkNnUVdKUVFCZ0drcXpoQUlBTUlESm15cWw5dGt1ZmdqZ3hE
    bEY1CmRjY3UrbkRnZ0FNQWdFQUxkbmFwV3dYUGJ2a2tWcDRaN3J2clh4V0RkZU
    NnZ2t3NlJpQWdZQUxvZEEKUVlCQUFBQ0FnUjRkQ0J3UVFFdkFWU291VzVjU0JD
    QXdKUUFHV0NBZ0hBQWJFdW13bUpIWEZkYmZ6ZApnQVFBQ0FnRUFDMndnQnJoRF
    R3a0VBZ0VBQWdFQU13d0NCZ3dkemQ0Z0NBUUZCZ2NrcmhBd0Rna0UKQUFBQ0FB
    QUFGUUZCZ2NrcmhBd0Rna0VBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQQ
    pNQkFBR0dDZ0FFckFRQUFBUUFDWmdFQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFB
    QUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQQ
    pVekFKQmdOVkJBZ1RBMkZuWVdOMFFYSmhiakNCYmpBZk5qQTVNelV3TVRFd01U
    TTFlVEFOCkJnTlZCQUdUQjJadVltOXNaQ0JzWVhOelpYTnpYMlpoYlhOMGJ5NX
    BibXhvZVEwMU1EQXdOREF3Ck56WTBNekEyTnpNMU1UVTJZamcwTVRCQ0FnRUFB
    MGRoQ0JNU0NVR0FUQkVsQUlBSHh4U2xHNGh6b2wKUG8wdkxjaUtHQUFNQWdFQU
    x0dC91N0h3a0VjQWdkWkhBQmdTQUFBQ0FnRUEwbk5wcm96R21iUm9QdmV3Clh6
    QlFCZ0dkcmRzQUlGSlVvMkVndEdzT0RBbFl1a0VBaWJkVEFhMThLd2g5eWJ6Nk
    J0eTYKY2dGNm96QkVBdjdWZXl0RUtxTkNnUVdKUVFCZ0drcXpoQUlBTUlESm15
    cWw5dGt1ZmdqZ3hEbEY1CmRjY3UrbkRnZ0FNQWdFQUxkbmFwV3dYUGJ2a2tWcD
    ...
```

**Cómo funcionan las etiquetas explícitas (`!!`):**

La sintaxis `!!` es una "etiqueta de tipo" (tag) en YAML. Permite especificar explícitamente el tipo de dato de un valor, lo que es útil en los siguientes casos:

1.  **Datos binarios:** Como acabamos de ver con `!!binary`.
2.  **Forzar un tipo:** Si YAML infiere un tipo que no es el que deseas. Por ejemplo, si tienes una cadena que parece un booleano o un número, puedes forzarla a ser una cadena:
    ```yaml
    cadena_true: !!str true # Forzar que 'true' sea una cadena, no un booleano
    id_numero_como_string: !!str 12345 # Forzar que '12345' sea una cadena
    ```
    (aunque para estas últimas, las comillas simples o dobles suelen ser suficientes y más legibles).

**Consideraciones y Desventajas:**

* **Legibilidad:** Los datos codificados en Base64 son ilegibles para los humanos, lo que va en contra del principio de "legibilidad humana" de YAML.
* **Tamaño del archivo:** La codificación Base64 aumenta el tamaño de los datos en aproximadamente un 33%. Para archivos binarios grandes, esto puede hacer que tu archivo YAML sea muy voluminoso y lento de cargar.
* **Manejo:** Requiere que el programa que lee el YAML decodifique los datos Base64 para convertirlos de nuevo a su formato binario original.
* **Alternativas:** Para la mayoría de los casos, es preferible:
    * **Referenciar el archivo:** Almacenar el archivo binario por separado y guardar su ruta o URL en el YAML.
        ```yaml
        logo_path: /imagenes/logo.png
        certificado_url: https://ejemplo.com/cert.pem
        ```
    * **Usar un sistema de gestión de activos:** Para grandes cantidades de datos binarios, es más eficiente usar sistemas de almacenamiento de objetos (como S3) o sistemas de archivos dedicados.

En resumen, la capacidad de YAML para manejar datos binarios es una característica poderosa, aunque de nicho. Es más probable que la encuentres en escenarios muy específicos, como la inclusión de pequeños iconos, hashes, o firmas digitales donde la autocontención del documento es crucial y el impacto en la legibilidad y el tamaño es mínimo.

---

[:point_up_2: Volver al Índice](README.md) | [:point_left: Capítulo 2](capitulo-2.md) | [:point_right: Capítulo 4](capitulo-4.md)
