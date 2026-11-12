# Informe de Análisis y Diseño: Sistema de Control de Rescate y Adopción Animal

## 1. Introducción y Objetivos
Este documento describe la arquitectura lógica, el modelado UML y la estrategia de control de versiones para el desarrollo del sistema de gestión de rescate y adopción de animales. El objetivo principal es coordinar eficazmente la recepción de avisos sobre animales en riesgo, la atención clínica y el acogimiento temporal.

---

## 2. Análisis de Requisitos

### 2.1. Actores del Sistema
* **Ciudadano / Reportante:** Actor externo que notifica casos de animales en situación de vulnerabilidad o riesgo en la vía pública.
* **Administrador del Refugio:** Gestiona los recursos operativos, asigna personal clínico/voluntarios y supervisa los procesos de adopción.
* **Voluntario / Veterinario:** Personal interno encargado de registrar el ingreso de especies, ejecutar tratamientos clínicos, actualizar el estado del animal y recibir notificaciones.

### 2.2. Requisitos Funcionales
* **RF-01:** El sistema debe permitir a cualquier ciudadano registrar un reporte de rescate indicando ubicación y descripción del caso.
* **RF-02:** El administrador debe poder asignar un veterinario o voluntario disponible a un caso activo.
* **RF-03:** El sistema debe registrar las fichas clínicas y evoluciones médicas de los animales rescatados según su especie (Perro, Gato, Fauna Silvestre).
* **RF-04:** El sistema debe gestionar el flujo completo de adopción (solicitud, evaluación y aprobación).

### 2.3. Requisitos No Funcionales (RNF)
* **RNF-01 (Disponibilidad):** El sistema debe garantizar una disponibilidad del 99% para la recepción de alertas críticas de rescate a través de la interfaz web/móvil.
* **RNF-02 (Rendimiento):** Las consultas de búsqueda de expedientes médicos y estados de adopción no deben superar los 2 segundos de respuesta.
* **RNF-03 (Seguridad):** El acceso a la base de datos y a las funciones administrativas del refugio debe estar protegido mediante autenticación cifrada por roles.
* **RNF-04 (Mantenibilidad):** El código fuente debe adherirse estrictamente a los principios SOLID y patrones de diseño orientados a objetos para facilitar la incorporación de nuevas especies o módulos.

---

## 3. Arquitectura del Sistema
El sistema se estructura bajo una **Arquitectura en Capas (Layered Architecture)** lógica, dividida en:
1. **Capa de Presentación:** Interfaces de usuario para ciudadanos, voluntarios y administradores.
2. **Capa de Negocio (Dominio):** Contiene la lógica de los expedientes de animales y la implementación de los patrones de diseño (*Factory Method*, *Observer*).
3. **Capa de Persistencia:** Gestiona la conexión única con la base de datos (*Singleton*) para almacenar historiales clínicos y reportes.

---

## 4. Modelado UML

### 4.1. Diagrama de Casos de Uso
Delimita las interacciones de los tres actores frente al sistema (registro de rescates, asignación de personal y gestión de fichas clínicas por especie).

### 4.2. Diagrama de Clases por Especie
Se implementa una jerarquía basada en una clase abstracta `ExpedienteAnimal` con sus atributos comunes (`idCaso`, `ubicacion`, `fechaRescate`, `estado`) y tres clases concretas por especie:
* **`ExpedientePerro`:** Atributos específicos como `raza` y `nivelEnergia`, con el método `ladrar()`.
* **`ExpedienteGato`:** Atributos específicos como `colorPelaje` y `temperamento`, con el método `maullar()`.
* **`ExpedienteFaunaSilvestre`:** Atributos específicos como `peligrosidad` y `habitatNatural`, con el método `liberar()`.

### 4.3. Diagrama de Secuencia
*Flujo del proceso de reporte y notificación:*
1. El **Ciudadano** envía el reporte de rescate mediante la interfaz.
2. El sistema invoca al `CreadorExpedientes` (Factory Method) para instanciar el tipo de expediente según la especie reportada.
3. Se registra la información en la base de datos utilizando la instancia de `ConexionBaseDatos` (Singleton).
4. Se activa el `NotificadorRescate` (Observer) para enviar alertas automáticas a los voluntarios y veterinarios disponibles.

---

## 5. Justificación Técnica de Patrones de Diseño
Los patrones implementados resuelven problemas arquitectónicos específicos del dominio:

* **1. Factory Method (`CreadorExpedientes` - Creacional):**
  * *Justificación:* La creación de expedientes varía según la especie (perros, gatos, fauna silvestre), cada uno requiriendo comportamientos distintos. Este patrón desacopla la lógica de instanciación del código principal, permitiendo añadir nuevas especies en el futuro sin alterar las clases existentes (cumpliendo el principio Abierto/Cerrado - OCP).
* **2. Singleton (`ConexionBaseDatos` - Creacional):**
  * *Justificación:* Controla de forma centralizada el acceso a la base de datos del refugio. Evita la apertura descontrolada de múltiples conexiones simultáneas y garantiza que todas las transacciones de historiales médicos utilicen la misma instancia de conexión activa de manera segura y eficiente.
* **3. Observer (`NotificadorRescate` - Comportamiento):**
  * *Justificación:* Cuando un caso de rescate ingresa o cambia de estado, múltiples actores (voluntarios y veterinarios) deben enterarse al instante. El patrón Observer permite que los suscriptores reaccionen automáticamente a los cambios en el expediente sin acoplar rígidamente el código de notificaciones al módulo principal de registro.

---

## 6. Conclusiones y Trazabilidad (Gitflow)
* El uso de Gitflow mediante ramas (`main`, `develop`, y `feature/`) aseguró un control de versiones estructurado y auditable en GitHub.
* La integración formal de la arquitectura en capas y los patrones de diseño dota al sistema de la escalabilidad y mantenibilidad requeridas para la gestión profesional de rescates animales.
