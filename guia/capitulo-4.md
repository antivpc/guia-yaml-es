[:point_up_2: Volver al Índice](README.md) | [:point_left: Capítulo 3](capitulo-3.md) | [:point_right: Capítulo 5](capitulo-5.md)

---

# Capítulo 4: Estructuras Avanzadas

## 1. Múltiples Documentos en un Solo Archivo

Una característica potente y a menudo subestimada de YAML es su capacidad para almacenar **múltiples documentos independientes dentro de un solo archivo**. Esto es increíblemente útil para organizar configuraciones complejas o para agrupar recursos relacionados que se procesan de manera secuencial o separada.

### El Separador de Documentos (`---`)

Para delimitar diferentes documentos dentro de un mismo archivo YAML, se utiliza el separador de tres guiones (`---`) en una línea por sí solo. Cada bloque de texto que precede o sigue a este separador se considera un documento YAML distinto.

* **Sintaxis:**

    ```yaml
    ---
    # Documento 1
    clave1: valor1
    lista1:
      - itemA
      - itemB
    ---
    # Documento 2
    clave2: valor2
    objeto2:
      subclave: subvalor
    ---
    # Documento 3 (opcionalmente puede comenzar con ---)
    final_doc: true
    ```

**Características y Usos:**

* **Inicio de un documento:** Es común (aunque no estrictamente obligatorio) que el primer documento de un archivo también comience con `---`. Esto mejora la claridad y ayuda a los analizadores a identificar el inicio del primer documento, especialmente si el archivo comienza con comentarios.
* **Final de un documento:** Un separador `---` indica el final del documento actual y el inicio del siguiente.
* **Separador de final de archivo (opcional):** Opcionalmente, puedes terminar un archivo YAML con `...` (tres puntos). Esto es más común cuando se transmiten múltiples documentos en un flujo, indicando el final de todos los documentos. Sin embargo, para la mayoría de los archivos estáticos, no es necesario.
    ```yaml
    ---
    documento_de_configuracion:
      api_key: abc123def456
    ---
    documento_de_datos:
      usuarios:
        - id: 1
          nombre: Alice
        - id: 2
          nombre: Bob
    ... # Opcional: indica el final del flujo de documentos
    ```

### Casos de Uso Comunes

La capacidad de tener múltiples documentos en un archivo es extremadamente valiosa en varios escenarios:

1.  **Configuración de Múltiples Entornos:**
    Puedes definir configuraciones diferentes para desarrollo, pruebas y producción en un solo archivo, separando cada entorno como un documento distinto. Esto facilita la gestión de configuraciones sin tener que mantener múltiples archivos.

    ```yaml
    # Configuración para el entorno de Desarrollo
    ---
    ambiente: development
    database:
      host: localhost
      port: 5432
    debug_mode: true
    ---
    # Configuración para el entorno de Producción
    ambiente: production
    database:
      host: prod-db.example.com
      port: 5432
    debug_mode: false
    ```
    Un script podría leer este archivo y cargar el documento específico según el entorno actual.

2.  **Manifiestos de Kubernetes:**
    En Kubernetes, es una práctica estándar definir múltiples recursos (como un `Deployment`, un `Service` y un `Ingress`) dentro de un único archivo `.yaml`, separados por `---`. Esto simplifica la aplicación y gestión de un conjunto de recursos relacionados.

    ```yaml
    # apiVersion, kind, metadata, spec, etc. para un Deployment
    ---
    # apiVersion, kind, metadata, spec, etc. para un Service
    ---
    # apiVersion, kind, metadata, spec, etc. para un Ingress
    ```

3.  **Definiciones de Docker Compose:**
    Aunque Docker Compose suele usar un solo documento para la definición de servicios, escenarios avanzados podrían usar múltiples documentos para configuraciones específicas o para extender una configuración base.

4.  **Generadores de Contenido/Sitios Estáticos:**
    Algunos generadores pueden usar múltiples documentos para separar metadatos de contenido, o para definir diferentes bloques de datos para una sola página.

5.  **Serialización de Objetos Múltiples:**
    Cuando serializas una secuencia de objetos de un programa a un archivo YAML, cada objeto puede ser serializado como un documento independiente para facilitar su procesamiento secuencial.

### Procesamiento de Múltiples Documentos

Cuando un analizador YAML encuentra `---`, sabe que el documento actual ha terminado y que el siguiente bloque de texto es un nuevo documento. La mayoría de las librerías de parseo de YAML tienen funciones específicas para cargar todos los documentos de un archivo en una lista o iterable.

Por ejemplo, en Python, en lugar de `yaml.safe_load(file)`, usarías `yaml.safe_load_all(file)` para obtener un iterador sobre cada documento.

```python
# Ejemplo de cómo se procesaría en Python (solo para ilustrar, no parte del markdown YAML)
import yaml

yaml_string = """
---
ambiente: development
database:
  host: localhost
  port: 5432
debug_mode: true
---
ambiente: production
database:
  host: prod-db.example.com
  port: 5432
debug_mode: false
"""

documentos = list(yaml.safe_load_all(yaml_string))

print(documentos[0])
# Salida: {'ambiente': 'development', 'database': {'host': 'localhost', 'port': 5432}, 'debug_mode': True}

print(documentos[1])
# Salida: {'ambiente': 'production', 'database': {'host': 'prod-db.example.com', 'port': 5432}, 'debug_mode': False}
```

La capacidad de encapsular múltiples documentos en un único archivo YAML simplifica la organización y el despliegue de configuraciones y datos complejos, manteniendo la legibilidad que caracteriza a YAML.

---

## 2. Anclas y Alias (Reutilización de Contenido)

En documentos YAML complejos o extensos, es común encontrarse con bloques de datos que se repiten. En lugar de copiar y pegar estos bloques, lo que introduce redundancia y aumenta el riesgo de inconsistencias si necesitas hacer un cambio, YAML ofrece un mecanismo elegante para reutilizar contenido: las **anclas (`&`)** y los **alias (`*`)**.

### Anclas (`&`)

Una **ancla** es una marca que le asignas a un nodo (un valor, un mapping o una lista) en tu documento YAML. Piensa en ella como una "etiqueta" o "nombre" que le das a un bloque de datos para poder referenciarlo más tarde.

* **Sintaxis:** Se define un ancla utilizando el símbolo **ampersand (`&`)** seguido de un nombre único para el ancla, y luego el nodo al que se adjunta.

    ```yaml
    # Definición de un ancla llamada 'config_base_db'
    db_default: &config_base_db
      host: localhost
      port: 5432
      user: admin
    ```

    En este ejemplo, el *mapping* que contiene `host`, `port` y `user` ha sido anclado con el nombre `config_base_db`.

### Alias (`*`)

Un **alias** es una referencia a un nodo que ha sido previamente definido con un ancla. Cuando un analizador YAML encuentra un alias, reemplaza ese alias con el contenido completo del nodo al que apunta el ancla.

* **Sintaxis:** Se referencia un ancla utilizando el símbolo **asterisco (`*`)** seguido del nombre del ancla.

    ```yaml
    # Uso de un alias para reutilizar 'config_base_db'
    database1: *config_base_db
    ```

    Cuando este YAML se procese, `database1` tendrá el mismo contenido que el nodo anclado `config_base_db`.

### Combinación de Anclas y Alias con Modificaciones (`<<: *`)

Una de las características más poderosas de las anclas y alias es que no solo permiten la reutilización exacta, sino que también permiten **fusionar** el contenido de un alias con un nuevo mapping, lo que significa que puedes reutilizar una base y luego sobrescribir o añadir propiedades.

Para esto, se utiliza la clave de fusión `<<: *nombre_del_alias`.

* **Sintaxis:**
    ```yaml
    nueva_clave:
      <<: *nombre_del_alias
      # Aquí puedes añadir nuevas propiedades o sobrescribir existentes
      propiedad_nueva: valor_nuevo
      propiedad_existente: nuevo_valor
    ```

* **Comportamiento de Fusión:**
    * Si una clave en el nuevo mapping ya existe en el mapping referenciado por el alias, el valor del nuevo mapping **sobrescribe** el valor del alias.
    * Si una clave en el nuevo mapping no existe en el alias, se **añade** al resultado final.

**Ejemplo Práctico Completo:**

Imaginemos que tenemos varias configuraciones de servicio que comparten muchas propiedades, pero difieren en algunas específicas.

```yaml
# Definición de anclas para configuraciones de servicio base
config_servicio_base: &base_web_service
  version: 1.0.0
  puerto: 80
  protocolo: http
  logs:
    nivel: info
    formato: json

# Definición de una ancla para el comportamiento en producción
produccion_default: &env_prod
  debug: false
  optimizado: true

# Definición de una ancla para el comportamiento en desarrollo
desarrollo_default: &env_dev
  debug: true
  reload_on_change: true

servicios:
  # Servicio de Frontend, usa la configuración base y la de desarrollo
  frontend_app:
    <<: *base_web_service
    <<: *env_dev # Fusiona primero env_dev
    puerto: 3000 # Sobrescribe el puerto de la base
    nombre: frontend-service
    logs:
      nivel: debug # Sobrescribe el nivel de log

  # Servicio de Backend, usa la configuración base y la de producción
  backend_app:
    <<: *base_web_service
    <<: *env_prod # Fusiona env_prod después
    puerto: 8080 # Sobrescribe el puerto de la base
    nombre: backend-service
    database:
      url: jdbc:postgresql://prod-db/app
      usuario: prod_user
```

**Resultado después de que el analizador procese el YAML:**

```yaml
servicios:
  frontend_app:
    version: 1.0.0
    puerto: 3000
    protocolo: http
    logs:
      nivel: debug
      formato: json
    debug: true
    reload_on_change: true
    nombre: frontend-service

  backend_app:
    version: 1.0.0
    puerto: 8080
    protocolo: http
    logs:
      nivel: info
      formato: json
    debug: false
    optimizado: true
    nombre: backend-service
    database:
      url: jdbc:postgresql://prod-db/app
      usuario: prod_user
```

Como puedes ver, los alias permiten construir configuraciones complejas a partir de bloques más pequeños y reutilizables, reduciendo la redundancia y simplificando las actualizaciones.

### Limitaciones y Consideraciones

* **Alcance:** Un ancla solo puede referenciar un nodo dentro del mismo documento YAML. No puedes referenciar un ancla definida en un documento diferente dentro del mismo archivo con `---` separadores.
* **Orden:** Los alias deben referenciar anclas que ya hayan sido definidas **antes** en el documento. Un alias no puede referenciar un ancla definida más adelante.
* **Complejidad:** Aunque poderosas, el uso excesivo o anidado de anclas y alias puede hacer que el YAML sea más difícil de seguir si no se documenta bien, ya que el valor final de un nodo no es inmediatamente obvio.
* **Tipos de datos:** Los alias pueden referenciar cualquier tipo de nodo: escalares, mappings o listas. Sin embargo, la fusión (`<<: *`) solo aplica a *mappings*. No puedes fusionar listas directamente usando esta sintaxis.

Dominar las anclas y los alias es un paso clave para escribir archivos YAML profesionales, especialmente en entornos de DevOps donde las configuraciones son a menudo grandes y deben mantenerse consistentes en diferentes despliegues.

---

## 3. Etiquetas de Tipos Explícitas (Tags)

YAML es notable por su capacidad de inferir automáticamente el tipo de dato de un valor (cadena, número, booleano, fecha, etc.) basándose en su formato y contexto. Sin embargo, hay ocasiones en las que la inferencia automática no es suficiente o no produce el tipo deseado. Para estos casos, YAML proporciona las **etiquetas de tipos explícitas (tags)**, que permiten al autor del documento especificar directamente el tipo de dato de un nodo.

### La Sintaxis de las Etiquetas (`!!`)

Una etiqueta de tipo explícita se indica utilizando el símbolo de doble exclamación (`!!`) seguido del nombre de la etiqueta (el tipo de dato) inmediatamente antes del valor del nodo.

* **Sintaxis:** `clave: !!tipo_de_dato valor`

YAML predefine una serie de etiquetas estándar para los tipos de datos básicos que ya hemos visto, y también para tipos más complejos. Cuando YAML infiere un tipo, en realidad está aplicando implícitamente una de estas etiquetas. Al usar la etiqueta explícitamente, estás anulando o confirmando esa inferencia.

### Etiquetas Estándar Comunes

Aquí te presentamos algunas de las etiquetas estándar más utilizadas y sus propósitos:

1.  **`!!str` (Cadena de Texto):**
    Fuerza que un valor sea interpretado como una cadena de texto, incluso si podría parecer un número, un booleano o una fecha. Esto es útil para evitar ambigüedades.

    ```yaml
    # YAML por defecto interpretaría estos como booleano y número
    cadena_booleana: "true" # Las comillas ya hacen que sea string, pero !!str lo clarifica
    id_como_string: !!str 12345 # Forzar que el número sea una cadena
    respuesta_no: !!str no # Forzar 'no' como cadena, no booleano
    ```

2.  **`!!int` (Entero):**
    Fuerza que un valor sea interpretado como un número entero. Aunque YAML suele inferirlos correctamente, podrías usarlo para mayor claridad o si hay un formato ambiguo.

    ```yaml
    version_int: !!int 1.0 # Podría generar un error si el procesador es estricto y 1.0 no es un entero puro.
    cantidad: !!int 100
    ```

3.  **`!!float` (Flotante):**
    Fuerza que un valor sea interpretado como un número de punto flotante.

    ```yaml
    temperatura_exacta: !!float 25
    pi_valor: !!float 3.14159
    ```

4.  **`!!bool` (Booleano):**
    Fuerza que un valor sea interpretado como un booleano. Aunque palabras como `true`, `false`, `yes`, `no` son inferidas, podrías usar la etiqueta para ser muy explícito o para manejar casos límite.

    ```yaml
    permitir_acceso: !!bool True
    desactivar_funcion: !!bool off
    ```

5.  **`!!null` (Nulo):**
    Fuerza que un valor sea nulo. Se usa cuando `null` o `~` no son lo suficientemente claros o se quiere ser extremadamente explícito.

    ```yaml
    sin_datos: !!null
    # Esto es equivalente a: sin_datos: null
    ```

6.  **`!!binary` (Binario):**
    Indica que el valor es una cadena codificada en Base64 que representa datos binarios. (Ya lo vimos en la sección 3.6).

    ```yaml
    imagen_icono: !!binary |
      iVBORw0KGgoAAAA...
    ```

7.  **`!!timestamp` (Marca de Tiempo/Fecha y Hora):**
    Fuerza que un valor sea interpretado como una fecha y/o hora.

    ```yaml
    fecha_registro: !!timestamp 2024-06-19 11:08:34
    fecha_nacimiento: !!timestamp 1985-03-10
    ```

8.  **`!!map` (Mapping/Objeto):**
    Fuerza que un nodo sea interpretado como un mapping (diccionario/objeto), incluso si su contenido podría estar vacío.

    ```yaml
    config_vacia: !!map {}
    ```

9.  **`!!seq` (Sequence/Lista):**
    Fuerza que un nodo sea interpretado como una secuencia (lista/array), incluso si su contenido podría estar vacío.

    ```yaml
    lista_vacia: !!seq []
    ```

### Etiquetas de Aplicación / Personalizadas

Además de las etiquetas estándar, YAML permite definir y usar **etiquetas personalizadas (Application-Specific Tags)**. Estas etiquetas suelen tener el formato `!nombre_etiqueta` (para etiquetas locales) o `!pfx!nombre_etiqueta` o incluso URI completas, y se utilizan para indicar tipos de datos específicos de tu aplicación o dominio que YAML no conoce por defecto.

Por ejemplo, podrías querer un tipo `Person` que tu aplicación mapee a un objeto de clase `Person` en tu código.

```yaml
# Definición de un objeto Person con una etiqueta personalizada
persona_data: !Person
  nombre: Carlos
  edad: 40
  ciudad: Madrid
```
Para que esto funcione, la librería YAML que uses en tu lenguaje de programación debe estar configurada para reconocer y saber cómo deserializar la etiqueta `!Person` en el objeto correspondiente.

### Cuándo Usar Etiquetas Explícitas

* **Claridad y Precisión:** Cuando la inferencia automática podría llevar a ambigüedad o quieres garantizar un tipo de dato específico, especialmente si el valor podría ser interpretado de múltiples maneras (ej. `"123"` vs. `123`).
* **Datos Binarios:** Obligatorio para incluir datos binarios codificados en Base64 (`!!binary`).
* **Tipos de Datos Personalizados:** Para serializar y deserializar objetos complejos específicos de tu aplicación que no se ajustan a los tipos básicos de YAML.
* **Interoperabilidad:** Asegurar que los datos sean interpretados de la misma manera por diferentes parsers YAML en diferentes lenguajes/sistemas.

En general, se recomienda dejar que YAML infiera los tipos siempre que sea posible para mantener el documento conciso y legible. Las etiquetas explícitas deben reservarse para cuando sean estrictamente necesarias para resolver ambigüedades o para definir tipos complejos de la aplicación.

---

## 4. Referencias Locales (Anclas y Alias) y Globales (URIs)

YAML ofrece mecanismos para reutilizar contenido y definir tipos de datos, lo que contribuye a la modularidad y la coherencia de los documentos. Hemos visto las anclas y alias (`&`, `*`) para la reutilización de contenido dentro de un mismo documento. Ahora, profundizaremos en su alcance y exploraremos cómo YAML puede interactuar con definiciones externas utilizando URIs para tipos de datos más complejos o esquemas.

### Repaso: Referencias Locales (Anclas `&` y Alias `*`)

Como ya lo vimos en la Sección 4.2, las anclas (`&`) y los alias (`*`) son la forma principal de reutilizar bloques de datos dentro del **mismo documento YAML**.

* **Ancla (`&nombre_ancla`):** Marca un nodo específico para que pueda ser referenciado.
* **Alias (`*nombre_ancla`):** Hace referencia al contenido del nodo marcado por el ancla.
* **Fusión (`<<: *nombre_ancla`):** Permite heredar y sobrescribir propiedades de un mapping referenciado.

**Recuerda las características clave de las referencias locales:**

* **Ámbito:** Son estrictamente locales al documento YAML en el que están definidas. No puedes referenciar un ancla de un documento a otro usando `---` separadores.
* **Reutilización de Contenido:** Su propósito principal es eliminar la duplicación de datos idénticos o casi idénticos.

**Ejemplo de repaso:**

```yaml
# Define una configuración de usuario estándar
usuario_base: &std_user
  rol: invitado
  activo: true
  permisos:
    - ver_perfil

usuarios:
  alice:
    <<: *std_user # Reutiliza la configuración base
    nombre: Alice Smith
    rol: editor # Sobrescribe el rol
    permisos:
      - ver_perfil
      - editar_contenido # Añade un permiso
  bob:
    <<: *std_user
    nombre: Bob Johnson
    activo: false # Sobrescribe el estado activo
```

### Referencias Globales (URIs para Etiquetas de Tipos)

Mientras que las anclas y alias manejan la reutilización de *contenido*, las **referencias globales** en YAML se refieren principalmente a la definición de **tipos de datos (tags)** que pueden ser reconocidos universalmente. Esto se logra usando **URI (Uniform Resource Identifiers)** como etiquetas de tipos.

La idea es que, en lugar de usar etiquetas cortas como `!!str` o `!Person`, puedes usar una URI completa que apunte a una especificación o definición externa de ese tipo de dato. Esto es parte de la extensibilidad del modelo de datos de YAML, permitiendo que documentos sean más semánticos y autocontenidos en su descripción de datos.

* **Sintaxis:** Se utilizan URI como etiquetas de tipo, a menudo con un prefijo que las acorta para el documento.

    ```yaml
    # Definición de un prefijo para una URI
    %TAG !tag:example.com,2024:product/

    # Uso de la etiqueta con el prefijo definido
    - !product/Book
      titulo: Cien años de soledad
      autor: Gabriel García Márquez
    - !product/Electronics
      nombre: Smartphone X
      marca: TechCorp
    ```

    En este ejemplo:
    * `%TAG` define un atajo (`!`) para la URI base `tag:example.com,2024:product/`.
    * `!product/Book` es una etiqueta de tipo que, internamente, se expandiría a `tag:example.com,2024:product/Book`. Esto le indica al procesador YAML que el siguiente mapping es de un tipo `Book` definido en un esquema particular.

**Propósito de las Referencias Globales/URIs en Etiquetas:**

* **Definición de Esquemas:** Permiten a los documentos YAML hacer referencia a esquemas de datos externos que definen la estructura y el significado de los tipos personalizados. Por ejemplo, una URI podría apuntar a un esquema JSON Schema o a una especificación YAML más compleja.
* **Interoperabilidad Semántica:** Cuando diferentes sistemas necesitan intercambiar datos YAML, el uso de etiquetas URI estándar o acordadas puede asegurar que todos los sistemas interpreten los datos de la misma manera, incluso si usan diferentes lenguajes o librerías.
* **Extensibilidad:** YAML puede ser extendido con cualquier tipo de dato que se necesite, simplemente definiendo una etiqueta URI para él.

**Cuándo se usan las referencias globales/URIs:**

* **Validación de Esquemas:** Cuando se usa YAML junto con herramientas de validación de esquemas (como JSON Schema para YAML), las URIs pueden vincular los datos a su definición de esquema.
* **Serialización de Objetos Complejos:** En sistemas donde objetos de una clase particular necesitan ser serializados a YAML y luego deserializados de nuevo, las etiquetas URI pueden mapear la representación YAML a la clase de objeto correcta.
* **Estándares de la Industria:** En dominios específicos donde se han definido estándares de datos basados en YAML, a menudo se utilizan URIs para los tipos de datos.

**Diferencia clave entre Local (Anclas/Alias) y Global (Tags/URIs):**

| Característica   | Anclas (`&`) y Alias (`*`)          | Etiquetas con URIs (`!`, `!!`)             |
| :--------------- | :---------------------------------- | :----------------------------------------- |
| **Propósito** | Reutilización de *contenido/datos* | Definición/Especificación de *tipos* |
| **Ámbito** | Estrictamente dentro del *mismo documento* | Globalmente referenciable (si el URI es accesible) |
| **Sintaxis** | `&nombre`, `*nombre`, `<<: *nombre` | `!tipo`, `!!tipo`, `%TAG` + `!prefijo`     |
| **Interacción** | El analizador *expande* el contenido | El analizador *identifica* el tipo; la aplicación lo procesa |

Aunque las referencias URI para etiquetas de tipos son una parte fundamental de la especificación YAML y su extensibilidad, en la práctica diaria de la mayoría de los usuarios de YAML (especialmente para archivos de configuración), son menos comunes que las anclas y alias. Sin embargo, su comprensión es clave para apreciar la robustez y la capacidad de extensión de YAML como lenguaje de serialización de datos.

---

## 5. Formato Compacto (Estilo de Flujo) vs. Bloque

YAML es conocido por su **estilo de bloque**, que utiliza la indentación para representar la estructura de los datos, priorizando la legibilidad humana. Sin embargo, YAML también soporta un **estilo de flujo (o compacto)**, que es más similar a la notación JSON, utilizando caracteres como corchetes `[]` para listas y llaves `{}` para mappings (objetos/diccionarios).

Entender ambos estilos te permite elegir la representación más adecuada para tus necesidades o comprender el YAML generado por diferentes herramientas.

### Estilo de Bloque (Block Style)

Este es el estilo predeterminado y más común en YAML. Se basa en la indentación para definir la jerarquía de los datos, lo que lo hace muy legible para estructuras anidadas.

* **Mappings (Objetos/Diccionarios):** Se representan con una clave seguida de dos puntos y un espacio (`: `), y los pares clave-valor anidados se indentan.
    ```yaml
    # Estilo de bloque para un mapping
    persona:
      nombre: Alice
      edad: 30
      ciudad: Nueva York
    ```

* **Secuencias (Listas/Arrays):** Se representan con un guion y un espacio (`- `) para cada elemento de la lista, y los elementos anidados se indentan.
    ```yaml
    # Estilo de bloque para una secuencia
    frutas:
      - manzana
      - pera
      - uva
    ```

* **Ventajas:**
    * **Alta Legibilidad:** La estructura visual es clara y fácil de seguir, especialmente para documentos complejos.
    * **Menos Caracteres de Delimitación:** No requiere corchetes ni llaves, lo que lo hace más conciso en general para estructuras anidadas.

* **Desventajas:**
    * **Sensible a la Indentación:** Errores de espacio o tabulación pueden romper el documento.
    * **Menos Compacto para Listas/Mappings Pequeños:** Para elementos muy cortos, puede ocupar más líneas.

### Estilo de Flujo (Flow Style)

El estilo de flujo es una forma más compacta y "en línea" de representar los datos, utilizando delimitadores explícitos como `[]` para secuencias y `{}` para mappings, similar a JSON.

* **Mappings (Objetos/Diccionarios):** Se encierran entre llaves `{}` y los pares clave-valor se separan por comas `,`.
    ```yaml
    # Estilo de flujo para un mapping
    persona: { nombre: Alice, edad: 30, ciudad: Nueva York }
    ```

* **Secuencias (Listas/Arrays):** Se encierran entre corchetes `[]` y los elementos se separan por comas `,`.
    ```yaml
    # Estilo de flujo para una secuencia
    frutas: [manzana, pera, uva]
    ```

* **Ventajas:**
    * **Compacto:** Ocupa menos líneas, ideal para pequeñas estructuras o cuando el espacio es limitado.
    * **Menos Sensible a la Indentación:** La estructura está definida por los delimitadores, no por la indentación (aunque la indentación sigue siendo necesaria para la clave principal que contiene el flujo).
    * **Familiar para Usuarios de JSON:** Si estás acostumbrado a JSON, el estilo de flujo es muy similar.

* **Desventajas:**
    * **Menos Legible:** Para estructuras anidadas o grandes, puede ser difícil de leer y editar.
    * **Más Caracteres de Delimitación:** Requiere llaves, corchetes y comas.

### Combinación de Estilos

Una de las grandes fortalezas de YAML es que **puedes mezclar y combinar el estilo de bloque y el estilo de flujo dentro del mismo documento**. Esto te permite usar el estilo más legible para las partes complejas y el estilo más compacto para las partes simples.

* **Ejemplo de Combinación:**

    ```yaml
    # Estilo de bloque principal
    configuracion:
      servidores:
        # Una lista de servidores en estilo de bloque
        - nombre: web1
          ip: 192.168.1.10
          roles: [frontend, backend] # Lista de roles en estilo de flujo
        - nombre: db1
          ip: 192.168.1.20
          roles: [database]

      # Parámetros generales en estilo de flujo para mayor concisión
      parametros_generales: { max_conexiones: 100, timeout: 60, debug_mode: false }

      # Un campo de estado simple en estilo de bloque
      estado_servicio: activo
    ```

    En este ejemplo:
    * `servidores` es una lista en estilo de bloque.
    * Cada servidor es un mapping en estilo de bloque.
    * `roles` dentro de cada servidor es una lista en estilo de flujo.
    * `parametros_generales` es un mapping completamente en estilo de flujo.

### Cuándo Usar Cada Estilo

* **Estilo de Bloque (Recomendado por Defecto):**
    * Para la mayoría de los archivos de configuración y datos estructurados.
    * Cuando la legibilidad y la editabilidad manual son prioritarias.
    * Para estructuras anidadas complejas.

* **Estilo de Flujo (Para Casos Específicos):**
    * Para estructuras muy pequeñas que pueden caber en una sola línea.
    * Cuando se necesita una representación más compacta para logging o transmisión.
    * Para elementos que son inherentemente de tipo JSON (si vienes de un contexto JSON).
    * Dentro de un documento más grande en estilo de bloque para hacer ciertas partes más concisas.

La flexibilidad de YAML para combinar estos estilos te da el poder de crear documentos que son tanto legibles como eficientes, adaptándose a la complejidad de los datos que necesitas representar.

---

[:point_up_2: Volver al Índice](README.md) | [:point_left: Capítulo 3](capitulo-3.md) | [:point_right: Capítulo 5](capitulo-5.md)
