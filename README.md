#                             **ACTIVIDAD BASE DE DATOS**













​                                                                                  **JEISON LEONARDO CRISTANCHO GARCÍA**

​                                                                                                                    **J3**

​                                                                                         **PEDRO FELIPE GÓMEZ BONILLA**













​                                                                                                      **CAMPUSLANDS**

​                                                                                                              **APOLO**

​                                                                                          **INTRODUCCION AL BACKEND**

​                                                                                                    **FLORIDABLANCA**

​                                                                                                                 **2025**

 

------



##                                                 **Tabla de Contenidos**





Introducción ............................................................ 3

Caso de Estudio ....................................................... 3

Planificación ........................................................... 3

Construcción del Modelo Conceptual ....................... 3

Construcción del Modelo Lógico .............................. 5

Normalización del Modelo Lógico ......................... 6

Construcción del Modelo Físico ............................. 7

Diagrama E-R ......................................................... 8

Tablas y Relaciones ................................................ 9

Inserción de Datos .................................................. 9

Referencias ............................................................. 10





















------





##                                                            **Introducción**



Este informe presenta el diseño completo de una base de datos orientada al registro y análisis de las muertes accidentales en Colombia durante el periodo 2015–2024. El trabajo se estructura en tres modelos de bases de datos; conceptual, lógico y físico, además, el proceso de normalización hasta la tercera forma normal (1FN, 2FN y 3FN). El objetivo es transformar un archivo de tablas de contenido en un sistema relacional claro y mantenible, garantizando integridad, coherencia y facilidad para la generación de consultas analíticas y estadísticas.



##                                                          **Caso de Estudio**

El conjunto de datos de referencia corresponde a registros de muertes accidentales en Colombia, con variables como: año del evento, departamento, causa, sexo y grupo de edad. El conjunto de datos original del DANE está en formato CSV y presenta redundancias y combinaciones de texto en celdas, lo cual dificulta la manipulación eficiente de la información. Por esta razón, se plantea un modelo relacional donde cada entidad y relación quede normalizada, evitando duplicidad y mejorando la consistencia de los datos. 



##                                                            **Planificación**

Las etapas del desarrollo se definieron así: (a) análisis de la base de datos primarios y su estructura, (b) diseño del modelo conceptual para identificar entidades y relaciones, (c) construcción del modelo lógico con definición de claves primarias y foráneas, (d) normalización progresiva hasta la tercera forma normal con ejemplos prácticos aplicados al modelo, y (e) construcción del modelo físico mediante SQL estándar.





##                                        **Construcción del Modelo Conceptual**

El modelo conceptual define las entidades fundamentales y sus relaciones. Cada entidad incluye atributos que representan las características clave de los datos. Este nivel de diseño se centra en el significado y estructura lógica de la información sin especificar implementaciones técnicas.

• REGIÓN (id_region, nombre_region) → Agrupa los departamentos del país.

• DEPARTAMENTO (id_departamento, nombre_departamento, id_region) → Unidad territorial administrativa.

• CAUSA (id_causa, nombre_causa, descripcion_causa) → Motivo de la muerte accidental.

• SEXO (id_sexo, tipo_sexo) → Clasificación biológica o administrativa.

• GRUPO_EDAD (id_grupo_edad, descripcion_grupo_edad, edad_min, edad_max) → Rango etario estandarizado.

• AÑO (id_anio, anio) → Periodo temporal de referencia.

• MUERTE (id_muerte, cantidad, id_causa, id_departamento, id_sexo, id_grupo_edad, id_anio) → Hecho principal del modelo.

Relaciones: REGIÓN 1—N DEPARTAMENTO; DEPARTAMENTO 1—N MUERTE; CAUSA 1—N MUERTE; SEXO 1—N MUERTE; GRUPO_EDAD 1—N MUERTE; AÑO 1—N MUERTE.

La entidad central ,muerte representa los registros agregados de muertes accidentales para una combinación específica de causa, sexo, grupo etario, año y departamento. La jerarquía REGIÓN → DEPARTAMENTO → MUERTE permite realizar análisis espaciales y demográficos, manteniendo independencia entre dimensiones.

```mermaid
flowchart TD
    A[Region] -->|Relation| B[Departamento]  
    A --- AA([id_region])
    A ---- AB([nombre_region])
    
    B --- BA([id_departamento])
    B ---- BB([nombre_departamento])
    B --- BC([codigo_dane_depto])
    B ---- BD([id_region])

    B[Departamento] ----->|Relation| C[Municipio]
    C --- CA([id_municipio])
    C ---- CB([id_departamento])
    C --- CC([nombre_municipio])
    
    C[Municipio] --->|Relation| D[Muerte]
    
    F[causa] -----> |Relation| D[Muerte]
    E[Sexo] ----->|Relation| D[Muerte]
    G[Persona] ---->|Relation| D[Muerte]
    H[Lugar_ocurrencia] ---->|Relation| D[Muerte]

    D --- DA([id_muerte])
    D ---- DB([fecha_muerte])
    D ----- DC([edad_en_anios])
    D ---- DD([id_municipio])
    D --- DE([id_sexo])
    D ---- DF([id_fecha])
    D ----- DG([id_lugar])
    D ---- DH([id_causa])

    E --- EA([id_sexo])
    E ---- EB([tipo_sexo])

    F --- FA([id_causa])
    F ---- FB([codigo_cie10])
    F --- FC([nombre_causa])
    F ---- FD([descripcion])

    G --- GA([id_persona])
    G ---- GB([nombres])
    G --- GC([apellidos])
    G ---- GD([tipo_documt])
    G --- GE([documt])

    H ---- HA([id_ocurrencias])
    H --- HB([nombre_ocurrencias])
```



##                                  

##                                             **Construcción del Modelo Lógico**

• REGION(id_region PK, nombre_region)

• DEPARTAMENTO(id_departamento PK, nombre_departamento, codigo_dane_depto, id_region FK→REGIÓN)

• CAUSA(id_causa PK, nombre_causa, descripcion_causa)

• SEXO(id_sexo PK, tipo_sexo)

• GRUPO_EDAD(id_grupo_edad PK, descripcion_grupo_edad, edad_min, edad_max)

• ANIO(id_anio PK, anio)

• MUERTES(id_muerte PK, id_causa FK, id_departamento FK, id_sexo FK, id_grupo_edad FK, id_anio FK, cantidad)

El modelo lógico define las claves primarias y foráneas, cumpliendo las tres primeras formas normales. Las relaciones 1:N se aplican entre las dimensiones y la tabla central de hechos (MUERTES).

![img](data:image/png;base64,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)





###                                                           **Entidades principales y relaciones**

Región
Atributos: *id_region* (PK), *nombre_region*. Define las grandes divisiones geográficas del país (por ejemplo: Caribe, Andina, Pacífica).
Se relaciona de forma 1:N con la tabla Departamento, ya que una región contiene varios departamentos.
Departamento
Atributos: *id_departamento* (PK), *nombre_departamento*, *codigo_dane_depto*, *id_region* (FK). Representa la unidad administrativa donde ocurrió el evento.
Su clave foránea *id_region* lo vincula jerárquicamente con la entidad Región.
La relación es N:1 hacia región y 1:N hacia la tabla Muerte.
Causa
Atributos: *id_causa* (PK), *nombre_causa*, *descripcion_causa*.Contiene los tipos de accidente o motivo de la muerte (por ejemplo: caída, ahogamiento, intoxicación).Se relaciona 1:N con Muerte, ya que una causa puede aparecer en muchos registros de muertes.
Sexo
Atributos: *id_sexo* (PK), *tipo_sexo*.Clasifica el sexo biológico de las víctimas (Masculino, Femenino, Noespecificado).Relación 1:N con Muerte.
Grupo_edad
Atributos: *id_grupo_edad* (PK), *descripcion_grupo_edad*, *edad_min*,*edad_max*.Define los rangos etarios (por ejemplo, 0–4, 5–9, 10–14 años).Relación 1:N con Muerte, ya que cada grupo agrupa muchos registros.
Año
Atributos: *id_anio* (PK), *anio*.Registra el periodo temporal de la observación.
Relación 1:N con Muerte, ya que cada año puede tener múltiples registros.

Muerte
Atributos: *id_muerte* (PK), *cantidad*, *id_causa*, *id_departamento*, *id_sexo*, *id_grupo_edad*, *id_anio*.Esta tabla integra todas las dimensiones del modelo: espacial (departamento/región), demográfica (sexo, grupo de edad), temporal (año) y causal (causa).El campo *cantidad* indica el número total de muertes para la combinación de factores referenciada por las claves foráneas.









##                                         **Normalización del Modelo Lógico**



### **Primera Forma Normal (1FN)**

La Primera Forma Normal (1FN) busca que todos los atributos sean atómicos, es decir, que cada campo contenga un solo valor indivisible. También elimina columnas que contengan listas o valores repetidos dentro de una misma celda. En la base original MUERTES existían combinaciones como 'Caída/Ahogamiento' o '15-19 años / 20-24 años', lo que no cumple con esta regla. Para cumplir la 1FN, se separaron estos datos en tablas individuales como CAUSA, SEXO y GRUPO_EDAD. De esta manera, la tabla MUERTES contiene únicamente claves numéricas (FK) y el valor agregado de 'cantidad'. El resultado es una estructura más limpia y apta para consultas SQL.

### **Segunda Forma Normal (2FN)**

La Segunda Forma Normal (2FN) elimina dependencias parciales, que ocurren cuando un atributo depende solo de una parte de una clave compuesta. En la versión inicial, el campo 'nombre_region' dependía únicamente del 'departamento', y no del resto de la clave compuesta (AÑO, DEPARTAMENTO, CAUSA, SEXO, GRUPO_EDAD). Para resolverlo, se creó la tabla REGIÓN con una relación jerárquica 1:N hacía DEPARTAMENTO. Ahora, los atributos de cada tabla dependen completamente de su clave principal. Este ajuste simplifica las relaciones y mejora la consistencia de la información geográfica.

### **Tercera Forma Normal (3FN)**

La Tercera Forma Normal (3FN) elimina dependencias transitivas, es decir, aquellas en las que un atributo no clave depende de otro atributo no clave. En el modelo inicial, 'nombre_region' podría inferirse a partir de 'nombre_departamento', lo que generaba redundancia. En el nuevo modelo, los datos se separan correctamente: REGIÓN contiene la información regional, DEPARTAMENTO solo depende de su propia PK, y MUERTES mantiene referencias mediante claves foráneas.





##                                            **Construcción del Modelo Físico**

```sql
CREATE TABLE region (
  id_region INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  nombre_region VARCHAR(100) NOT NULL
);

CREATE TABLE departamento (
  id_departamento INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  nombre_departamento VARCHAR(120) NOT NULL,
  codigo_dane_depto VARCHAR(10),
  id_region INTEGER NOT NULL,
  FOREIGN KEY (id_region) REFERENCES region(id_region)
);

CREATE TABLE causa (
  id_causa INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  nombre_causa VARCHAR(150) NOT NULL,
  descripcion_causa VARCHAR(255)
);

CREATE TABLE sexo (
  id_sexo INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  tipo_sexo VARCHAR(30) NOT NULL
);

CREATE TABLE grupo_edad (
  id_grupo_edad INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  descripcion_grupo_edad VARCHAR(60),
  edad_min INTEGER,
  edad_max INTEGER
);

CREATE TABLE anio (
  id_anio INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  anio INTEGER NOT NULL
);

CREATE TABLE muertes (
  id_muerte INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  id_causa INTEGER NOT NULL,
  id_departamento INTEGER NOT NULL,
  id_sexo INTEGER NOT NULL,
  id_grupo_edad INTEGER NOT NULL,
  id_anio INTEGER NOT NULL,
  cantidad INTEGER NOT NULL,
  FOREIGN KEY (id_causa) REFERENCES causa(id_causa),
  FOREIGN KEY (id_departamento) REFERENCES departamento(id_departamento),
  FOREIGN KEY (id_sexo) REFERENCES sexo(id_sexo),
  FOREIGN KEY (id_grupo_edad) REFERENCES grupo_edad(id_grupo_edad),
  FOREIGN KEY (id_anio) REFERENCES anio(id_anio)
);
```

##                                                       





##                                                               **Diagrama E-R**

El diagrama entidad-relación posiciona a la tabla MUERTES como entidad central, conectada con las dimensiones CAUSA, SEXO, GRUPO_EDAD, AÑO y DEPARTAMENTO. La entidad DEPARTAMENTO se enlaza con REGIÓN.



 

##                                                          **Tablas y Relaciones**

Las relaciones entre las tablas se definen mediante claves primarias y foráneas, garantizando la integridad referencial. El diseño propuesto permite analizar los datos desde múltiples perspectivas: por causa, género, edad, ubicación y año, sin redundancia ni pérdida de consistencia.



##                                                         **Inserción de Datos**

INSERT INTO region (nombre_region) VALUES ('Caribe');
INSERT INTO departamento (nombre_departamento, codigo_dane_depto, id_region) VALUES ('Atlántico', '08', 1);
INSERT INTO causa (nombre_causa, descripcion_causa) VALUES ('Caída', 'Caída desde altura o superficie resbalosa');
INSERT INTO sexo (tipo_sexo) VALUES ('Femenino');
INSERT INTO grupo_edad (descripcion_grupo_edad, edad_min, edad_max) VALUES ('15-19', 15, 19);
INSERT INTO anio (anio) VALUES (2015);

INSERT INTO muertes (id_causa, id_departamento, id_sexo, id_grupo_edad, id_anio, cantidad)
VALUES (1);





------





## **Referencias**

DANE – Muertes accidentales en Colombia (2015–2024). Documentación y datos abiertos.
Elmasri & Navathe. Sistemas de Bases de Datos. Pearson.
Documentación SQL Estándar (ANSI/ISO).

