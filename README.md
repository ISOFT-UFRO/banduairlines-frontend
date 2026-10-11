# BanduAirlines · Frontend

BanduAirlines es una plataforma web para buscar, reservar, pagar y gestionar pasajes de una aerolínea nacional de bajo costo, desarrollada como proyecto semestral de Ingeniería de Software (ICC712), 2° semestre 2026, en la Universidad de La Frontera. Este repositorio contiene la aplicación web, desarrollada en Vue.js.

# Guía de Arquitectura y Trabajo

Este repositorio utiliza una arquitectura modular diseñada para que los múltiples equipos puedan trabajar en paralelo sobre el mismo cliente Vue.js sin generar conflictos masivos de código. 

## Arquitectura de Carpetas
El código fuente (`src/`) se divide en dos áreas de responsabilidad estrictas:

* **`core/` (Exclusivo de Integración):** Contiene la configuración global, las plantillas base (layouts), utilidades transversales, el cliente HTTP preconfigurado y los componentes visuales genéricos (botones, modales, alertas). Ningún equipo de módulo de negocio debe modificar esta carpeta.
* **`modules/` (Espacio de los equipos):** Contiene una subcarpeta para cada módulo (Cuentas, Reservas, Pagos, Check-in, etc.). Todo tu código (vistas, componentes específicos, servicios y rutas) debe vivir **únicamente** dentro de tu directorio.

## Reglas de Convivencia entre Módulos
1. **Aislamiento total:** Un módulo no puede importar vistas, componentes ni funciones directamente desde la carpeta de otro módulo. Si necesitas información de otra área, consúltala a través de la API del backend o comunícate con Integración para definir una estrategia en el estado global del frontend.
2. **Componentes compartidos:** Si tu equipo crea un elemento visual o una función que otros equipos también necesitan, solicita a Integración que lo evalúe, lo estandarice y lo mueva a `core/`.
3. **Configuración del proyecto:** Los equipos no deben modificar archivos raíz como `vite.config.js`, `package.json` o `main.ts` sin previa coordinación.

## Gestión de Rutas Descentralizadas
Para evitar que los desarrolladores editen un mismo archivo de rutas al mismo tiempo y destruyan el historial de Git, el enrutamiento funciona de forma descentralizada:

* Cada equipo debe mantener un archivo `routes.ts` exclusivamente dentro de la carpeta de su módulo.
* En ese archivo solo se exporta el arreglo de rutas que pertenecen a su respectiva funcionalidad.
* El archivo principal de Vue Router (`src/router/index.ts`) ya está programado por Integración para recolectar y fusionar automáticamente las rutas de todas las carpetas. No necesitas ir a inyectar tus vistas allí.

## Flujo de Trabajo y Ramas
* **La regla de oro:** Nadie escribe directamente en la rama `develop` ni en `main` 
* **Desarrollo de tareas:** Cada cambio nace en una rama `feature/{nombre-modulo}-{nombre-de-la-funcionalidad}` creada desde `develop`
* **Entrega de avances:** Al terminar, debes abrir un Pull Request hacia `develop` Al abrir el Pull Request, GitHub Actions ejecuta la CI de este repositorio (lint y build en el frontend); si falla, el Pull Request queda marcado en rojo y no se mezcla.
* **Paso a producción:** Integración es el único módulo que mezcla `develop` en `main`.