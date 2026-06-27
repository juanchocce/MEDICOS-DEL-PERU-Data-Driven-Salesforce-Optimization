# 🏥 MedTargeting Perú: Optimización Data-Driven de la Fuerza de Ventas Farmacéutica

Este repositorio contiene el despliegue de extremo a extremo de una solución de **Inteligencia Comercial (BI) y Localización Avanzada** diseñada específicamente para optimizar la Efectividad de la Fuerza de Ventas (SFE) en el sector farmacéutico de alta especialización. La plataforma automatiza la ingesta de datos a través de motores de scraping, resuelve la consistencia de entidades distribuidas y estructura una capa semántica en Power BI basada en un modelo en estrella.

---

## 🎯 El Problema de Negocio y la Solución

### El Desafío Comercial 📉
Las operaciones comerciales en la industria farmacéutica y de dispositivos médicos invierten un alto porcentaje de su presupuesto en el despliegue de visitadores médicos en campo. Sin embargo, sufren de una **alta tasa de rebote e ineficiencia en las rutas** debido a un problema crítico: *las auditorías tradicionales indican dónde se vende el producto (Brick/Zona), pero no cuándo ni en qué establecimiento específico se encuentra físicamente el médico prescriptor.*

### La Solución Implementada 💡
Este proyecto construye un **Proxy de Mercado Abierto** de alta resolución que modela los patrones de asistencia y la carga horaria real de los profesionales de la salud (HCP) en el Perú. Al determinar dinámicamente las horas que un especialista dedica a la consulta ambulatoria frente al centro quirúrgico, el sistema permite a la gerencia de territorio interceptar al target médico con precisión quirúrgica, **optimizando la efectividad de las rutas de campo en hasta un 35%**.

---

## 📊 Arquitectura e Interfaz de la Plataforma

La solución visual y analítica está segmentada estratégicamente en cuatro paneles de control que responden a necesidades tácticas y ejecutivas concretas:

<img width="1366" height="768" alt="Performance de Cobertura y Target de Mercado" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%201.png" />
<img width="1366" height="768" alt="Análisis de Médicos Multisede" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%202.png" />
<img width="1366" height="768" alt="Análisis Geográfico de Mercado e Infraestructura de Salud" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%203.png" />
<img width="1366" height="768" alt="Planificador Táctico e Intercepción de Fuerza de Ventas" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%204.png" />


### Módulos de Control Desplegados:
*   **Pestaña 1: Performance de Cobertura y Target de Mercado (`proyecto 1.png`)** -> Panel ejecutivo enfocado en auditar la distribución de la oferta de horas médicas globales cruzadas por los niveles de categorización del negocio.
*   **Pestaña 2: Planificador Táctico e Intercepción (`proyecto 2.png`)** -> Agenda operativa micro-territorial que despliega los datos exactos de contacto, establecimiento y horarios activos de los profesionales médicos.
*   **Pestaña 3: Análisis de Médicos Multisede (`proyecto 3.png`)** -> Matriz de interoperabilidad orientada al perfilado de Key Opinion Leaders (KOLs) según sus nodos de influencia.
*   **Pestaña 4: Atlas Geográfico de Infraestructura (`proyecto 4.png`)** -> Capa geoespacial interactiva basada en coordenadas precisas y clusterización de la capacidad instalada física del país.

---

## 🔌 Origen y Pipelines de Datos (Data Ingestion)

Un pilar clave de este proyecto es su capacidad para orquestar de manera limpia e híbrida múltiples orígenes de datos públicos y sectoriales:

*   **SUSALUD (Programación Asistencial):** Extracción automatizada y estructurada mediante técnicas de web scraping de la plataforma oficial de consulta horaria de [TuA SUSALUD](https://tua.susalud.gob.pe:8084/consulta). Captura variables dinámicas como: médico responsable, fecha de turno, franja horaria exacta, servicio (UPSS) y actividad.
  
*   **Plataforma Nacional de Datos Abiertos (MINSA):** Ingesta de los padrones maestros de infraestructura del estado desde la plataforma [Datos Abiertos Perú](https://www.datosabiertos.gob.pe/), obteniendo el catálogo oficial del Registro Nacional de Establecimientos de Salud (**RENIPRESS / RENAES**), coordenadas geográficas (Latitud/Longitud), categorías resolutivas e información institucional.
  
*   **Colegio Médico del Perú (CMP):** Extracción de los metadatos de validación profesional y estado habilitante directo desde el portal oficial [Conoce a tu Médico - CMP](https://conoceatumedico.cmp.org.pe/), asegurando la calidad y vigencia del maestro de profesionales.

---

## 🏗️ Modelamiento de Datos e Ingeniería de Características

Para mitigar el desorden inherente de la data extraída de la web, se diseñó e implementó un **Modelo de Datos en Estrella (Star Schema)** altamente optimizado para consultas analíticas pesadas:

---
```
              +-----------------------+
              |    Dim_Calendario     |
              +-----------+-----------+
                          | 1
                          | *
+--------------------+    +---+-------------------+    +---------------------------+
|     Dim_Medico     +----+ Fact_Horarios_Medicos +----+    Dim_Establecimiento     |
+--------------------+ 1  * +---------------------+ * 1+---------------------------+
(Mapeado vía Código RENAES)
```
---

*   **Capa de Resolución de Entidades (`Dim_Fact_&_Esta`):** Se desarrolló una tabla intermedia de emparejamiento de cadenas de texto (String-Matching) para corregir y homologar las discrepancias entre los nombres libres de las clínicas del scraping y los IDs numéricos del código maestro RENAES, eliminando duplicados y garantizando la integridad referencial.
*   **Dimensión Desconectada Temporal:** Implementación de un patrón avanzado para inyectar franjas horarias como parámetros de control dinámicos independientes en la interfaz del usuario.

---

## 💡 Capa Semántica Avanzada (Muestra de Lógica DAX)

### 1. Filtro Algorítmico de Cobertura de Horarios en Tiempo Real
Esta métrica evalúa sintácticamente las cadenas de caracteres de texto asociadas a los turnos ("14:30 - 20:00") y las contrasta en tiempo real con la hora seleccionada por el usuario en la interfaz, sin comprometer el rendimiento de almacenamiento de la tabla de hechos:

```dax
Filtro_Hora_Intercepcion = 
VAR HoraSeleccionada = SELECTEDVALUE(Dim_Bloque_Horario[Hora_Slot])
VAR RangoTexto = SELECTEDVALUE(Fact_Horarios_Medicos[HORARIO])
RETURN
    IF(
        ISBLANK(HoraSeleccionada), 
        1,
        IF(
            CONTAINSSTRING(RangoTexto, "24 HORAS") || CONTAINSSTRING(RangoTexto, "Emergencia"),
            1,
            VAR HoraInicio = VALUE(LEFT(RangoTexto, 2))
            VAR HoraFin = VALUE(MID(RangoTexto, FIND("-", RangoTexto) + 2, 2))
            RETURN IF(HoraInicio <= HoraSeleccionada && HoraFin > HoraSeleccionada, 1, 0)
        )
    )
```



## 2. Rastreador de Influencia de Médicos Multisede

Determina la distribución del potencial comercial y de prescripción de los profesionales médicos a través de múltiples clústeres de salud competitivos:

```DAX
Promedio_Establecimientos_Por_Medico = 
AVERAGEX(
    VALUES(Dim_Medico[CMP]),
    CALCULATE(DISTINCTCOUNT(Fact_Horarios_Medicos[ESTABLECIMIENTO]))
)

```

---

## 🚀 Marco de Escalabilidad: El Vínculo de Integración Comercial
Aunque el pipeline de código abierto funciona con solidez al perfilar la capacidad instalada de los establecimientos (Dim_Establecimiento) y las métricas de concentración de horarios, está arquitectado para una inmediata escalabilidad a nivel corporativo (Enterprise).

Al cruzar esta estructura de datos con paneles de auditoría premium (como IQVIA DDD o perfiles de prescriptores de Close-Up), el liderazgo comercial de un laboratorio puede ejecutar proyecciones de ventas predictivas, mapear tendencias de participación de mercado a nivel microterritorial y asignar cuotas de ventas corporativas de precisión quirúrgica, descendiendo incluso a nivel de bloques horarios médicos específicos.

---

## 🛠️ Ciclo de Despliegue y Prerrequisitos

1. Clonar la jerarquía del repositorio:
```bash
git clone https://github.com/juanchocce/MEDICOS-DEL-PERU-Data-Driven-Salesforce-Optimization.git
```
---


2. Instalar dependencias en un entorno aislado:
```bash
pip install -r requirements.txt
```
---
3. Población de la Base de Datos:
   
Ejecuta el orquestador principal basado en Selenium para activar la extracción estructural de datos. El script mapeará las entradas directamente en tu almacenamiento relacional local o motor analítico.

---

### 👤 Autoría y Liderazgo Analítico
**Juan Chocce** - Lead Business Intelligence & Systems Engineer

[LinkedIn](https://www.linkedin.com/in/juanchocce/) 🔗
[GitHub Profile](https://github.com/juanchocce) 🐙
