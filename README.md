# 🎓 Sistema de Gestión y Repositorio de Tesis (Backend)

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg?style=for-the-badge&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-4.0+-092E20.svg?style=for-the-badge&logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/Django_REST-ff1709.svg?style=for-the-badge&logo=django&logoColor=white)
![AWS](https://img.shields.io/badge/AWS_S3-232F3E.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)

> **API RESTful robusta y modular diseñada para la gestión segura, almacenamiento y visualización de documentos académicos universitarios.**

Este proyecto constituye el núcleo lógico (Backend) de una plataforma de titulación. Fue diseñado bajo principios de escalabilidad y seguridad para administrar el ciclo de vida completo de una tesis, desde la solicitud del alumno hasta su validación y publicación por parte del cuerpo docente.

---

## 🏛️ Arquitectura del Sistema

El software sigue una **Arquitectura Modular** basada en aplicaciones desacopladas, lo que permite un mantenimiento eficiente y escalabilidad a largo plazo. Cada módulo encapsula una lógica de negocio específica:

### 📂 Estructura de Módulos (Apps)

* **🔐 UserApp (Gestión de Identidad)**
    * Manejo de autenticación personalizada.
    * Perfiles de usuario extendidos mediante relaciones `OneToOne` para integridad de datos.
    * Sistema de Roles y Grupos (RBAC) para diferenciar permisos entre Alumnos, Profesores y Directores.

* **📚 AcademicApp (Estructura Universitaria)**
    * Modelado jerárquico de la institución: *Áreas -> Carreras -> Asignaturas -> Secciones*.
    * Gestión de la carga académica y asignación de docentes a secciones específicas.

* **📄 DocumentApp (Core del Negocio)**
    * Motor de gestión documental.
    * Control de subida de archivos PDF con encriptación y almacenamiento en la nube (AWS S3).
    * Flujos de estado (Máquina de estados) para el proceso de aprobación de tesis.
    * Generación de estadísticas de visualización y descarga en tiempo real.

---

## 🚀 Características Técnicas Destacadas

Este backend implementa patrones de diseño y soluciones avanzadas para problemas comunes en desarrollo web:

### 1. Seguridad y Autenticación (JWT & Cookies)
A diferencia de las implementaciones estándar que almacenan tokens en `localStorage` (vulnerables a XSS), este sistema implementa **JSON Web Tokens (JWT)** almacenados estrictamente en **HttpOnly Cookies**.
* **Protección:** Cookies configuradas con flags `Secure` y `SameSite`.
* **Rotación:** Manejo automático de `Refresh Tokens` para mantener sesiones seguras.

### 2. Optimización de Consultas (Database Performance)
Se abordó el problema de **"N+1 Queries"** típico en ORMs mediante el uso estratégico de `select_related` y `prefetch_related` en los ViewSets.
* **Resultado:** Reducción drástica del tiempo de respuesta en endpoints que listan documentos con múltiples relaciones (Autor, Profesor Guía, Área, Carrera).

### 3. Automatización mediante Signals
El sistema reacciona a eventos de la base de datos utilizando el patrón Observador (`Django Signals`) para desacoplar la lógica:
* **Asignación Dinámica de Roles:** Si un profesor es asignado a una tesis, el sistema eleva sus privilegios automáticamente a "Profesor Guía".
* **Estadísticas:** Creación automática de registros de métricas al momento de subir un nuevo documento.

### 4. Gestión de Archivos en la Nube
Integración con **AWS S3** para el almacenamiento de archivos estáticos y media (PDFs de tesis).
* Esto asegura que el servidor de aplicación (Django) se mantenga ligero (Stateless), delegando la carga y entrega de archivos pesados a la infraestructura de Amazon.

---

## 🛠️ Stack Tecnológico

* **Lenguaje:** Python 3.x
* **Framework Web:** Django & Django Rest Framework (DRF)
* **Base de Datos:** MySQL
* **Infraestructura:** Docker & Nginx (Proxy Inverso)
* **Cloud:** AWS S3 (Storage)
* **Librerías Clave:**
    * `djoser`: Gestión de usuarios y autenticación.
    * `django-storages`: Conexión con servicios cloud.
    * `Pillow`: Procesamiento de imágenes.
    * `django-filter`: Filtrado avanzado de APIs.

---

## 🔌 Diseño de la API (Endpoints Principales)

La API está diseñada siguiendo los principios REST, utilizando verbos HTTP estándar y códigos de estado adecuados.

| Módulo | Endpoint | Descripción | Acceso |
| :--- | :--- | :--- | :--- |
| **Auth** | `/api/auth/jwt/create/` | Login seguro y seteo de cookies | Público |
| **Users** | `/api/users/profile/me/` | Obtener datos del usuario actual | Auth |
| **Docs** | `/api/docs/document/` | Listar y filtrar tesis aprobadas | Público |
| **Docs** | `/api/docs/document/{id}/` | Detalle y descarga segura de tesis | Auth/Público |
| **Stats** | `/api/docs/statistics/` | Dashboard de métricas de uso | Admin/Director |
| **Academic** | `/api/academic/career/` | Listado de estructura académica | Auth |

---

## ✒️ Autores

**Cristian Quiroz, Franco Garrido , Matias Zurita**
