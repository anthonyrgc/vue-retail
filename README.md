# Prueba técnica de vue3

Este proyecto es una aplicación web creada con **Vue 3** y **Vite** que muestra una lista de productos con filtros por categoría y rango de precio. Incluye paginación, animaciones, guardado de preferencias en localStorage y estadísticas básicas.

---

## Características principales

- Listado de productos obtenidos desde una API externa.
- Filtros dinámicos por categoría y rango de precio.
- Guardado y carga de preferencias de filtros usando `localStorage`.
- Paginación con navegación entre páginas.
- Animaciones suaves al cambiar filtros o páginas.
- Visualización de estadísticas como total de productos, categorías únicas y precio promedio.

---

## Requisitos previos

- Node.js (versión 14 o superior recomendada)
- npm (viene con Node.js)

---

## Instalación

1. Clona este repositorio o descarga el código.
2. Abre una terminal en la carpeta raíz del proyecto.
3. Ejecuta para instalar dependencias:

```sh
npm install
```

## Desarrollo

Para correr la app en modo desarrollo con recarga en caliente:

```sh
npm run dev
```

### Producción

Para compilar y minificar la aplicación para producción:

```sh
npm run build
```

### Linting

Para analizar y corregir problemas de estilo y errores potenciales en el código usando ESLint:

```sh
npm run lint
```

## Tecnologías usadas

- **Vue 3**: Framework progresivo de JavaScript para construir interfaces de usuario.
- **Vite**: Herramienta de construcción rápida para proyectos frontend.
- **Axios**: Cliente HTTP para realizar peticiones a APIs.
- **Tailwind CSS**: Framework CSS para diseño rápido y responsive con utilidades.
- **ESLint**: Herramienta para mantener la calidad y consistencia del código.
- **JavaScript (ES6+)**: Lenguaje principal para el desarrollo frontend.

## Estructura básica

El proyecto sigue una estructura modular para facilitar el mantenimiento y escalabilidad:

- `src/components` — Componentes Vue reutilizables (ProductCard, ProductPagination, ProductStatistics)
- `src/App.vue` — Componente principal con lógica de filtros, paginación y animaciones
- `public/` — Archivos estáticos

## Decisiones técnicas relevantes

- **Uso de Vue 3 con Composition API:**  
  Se eligió Vue 3 y su Composition API para aprovechar una mejor organización del código y una mayor flexibilidad en la gestión del estado y la lógica reactiva.

- **Vite como herramienta de build:**  
  Se usó Vite para un desarrollo rápido con hot-reload eficiente y una configuración sencilla.

- **LocalStorage para persistencia de filtros:**  
  Para mejorar la experiencia del usuario, las preferencias de búsqueda se guardan en localStorage, permitiendo que los filtros persistan entre sesiones.

- **Animaciones con `<Transition>` y `<TransitionGroup>` de Vue:**  
  Se implementaron animaciones de entrada/salida para mejorar la experiencia visual al cambiar páginas o filtros, usando clases CSS personalizadas para desvanecimiento y desplazamiento.

- **Separación en componentes reutilizables:**  
  Componentes como ProductCard, ProductPagination y ProductStatistics permiten modularizar la aplicación, facilitando mantenimiento y escalabilidad.

- **Uso de API pública para datos:**  
  Se usaron endpoints públicos para productos y categorías, asegurando datos reales para la prueba y demostración.

- **Tailwind CSS para estilos rápidos y responsivos:**  
  El proyecto utiliza Tailwind para facilitar el diseño responsive y acelerar el desarrollo con utilidades CSS predefinidas.
