# TalentoStore — Carrito de compras (Vue 3 + Vite)

Aplicación de carrito de compras desarrollada en Vue 3 con Vite para practicar estado reactivo, lógica de negocio y construcción de interfaz, basada en el ejercicio TalentoStore del módulo 6.

## Objetivo

Implementar un carrito de compras totalmente **reactivo**, separando claramente:

- **Rol A (Estado & Lógica)**: manejo de datos y reglas del negocio.
- **Rol B (Interfaz & Cliente)**: renderizado de la UI y formularios.

Usando:

- `reactive` para la tienda y el cliente.
- `ref` para estados simples (mensaje de éxito).
- `computed` para totales y validaciones.
- `v-model` en el formulario.
- `v-for` y eventos (`@click`) para catálogo y carrito.

## Estado y lógica (Rol A)

En `App.vue` se define el estado y la lógica principal:

- `tienda` (`reactive`):

  - `catalogo`: lista de productos, cada uno con:
    - `id`
    - `nombre`
    - `precio`
  - `carrito`: array de ítems con:
    - `id`
    - `nombre`
    - `precio`
    - `cantidad`

- `cliente` (`reactive`):

  - `nombre`
  - `email`

- Funciones:

  - `agregarAlCarrito(producto)`  
    - Agrega un producto al carrito o incrementa su cantidad si ya existe.
  - `quitarDelCarrito(id)`  
    - Elimina un ítem del carrito filtrando por `id`.
  - `cambiarCantidad(id, delta)`  
    - Modifica la cantidad (+1 / −1) respetando un mínimo de `1`.

- Computeds:

  - `totalItems`  
    - Suma todas las cantidades del carrito.
  - `subtotal`  
    - Suma de `precio * cantidad` por cada ítem.
  - `descuento`  
    - 10% del subtotal cuando `subtotal >= 50000`, si no, 0.
  - `total`  
    - `subtotal - descuento`.
  - `emailValido`  
    - Validación básica de email con expresión regular.
  - `puedeComprar`  
    - `true` solo si:
      - Hay ítems en el carrito.
      - El nombre no está vacío.
      - El email tiene formato válido.

- Estado adicional:

  - `compraFinalizada` (`ref`) para controlar el mensaje de éxito al finalizar la compra.

Al final de la compra se muestra un mensaje de éxito, se vacía el carrito y se reinicia el formulario del cliente.

## Interfaz (Rol B)

El `<template>` de `App.vue` se organiza en tres paneles:

- **Catálogo**

  - Renderizado con `v-for` a partir de `tienda.catalogo`.
  - Muestra nombre y precio de cada producto.
  - Botón “+ Agregar” que llama a `agregarAlCarrito(producto)`.

- **Carrito**

  - Muestra el número total de ítems usando `totalItems`.
  - Si el carrito está vacío, muestra un mensaje informativo.
  - Renderiza los ítems con `v-for` sobre `tienda.carrito`.
  - Controles:
    - Botones “−” y “+” que usan `cambiarCantidad(id, delta)`.
    - Botón “Quitar” que usa `quitarDelCarrito(id)`.
  - Sección de totales:
    - `Subtotal`
    - `Descuento`
    - `Total`  
    Todos calculados con `computed`.

- **Datos del cliente**

  - Inputs de nombre y email con `v-model` sobre `cliente.nombre` y `cliente.email`.
  - Botón “Finalizar compra”:
    - Usa `:disabled="!puedeComprar"` para controlar disponibilidad.
    - Ejecuta `finalizarCompra` al hacer clic.
  - Mensajes:
    - Ayuda: “Completa carrito y datos” cuando `puedeComprar` es `false`.
    - Mensaje de éxito con el nombre del cliente y el total pagado cuando `compraFinalizada` es `true`.

## Estilos

Los estilos se definen en `App.vue` usando `<style scoped>` y se complementan con un `style.css` global:

- Layout principal (`.app`) con `display: grid`:

  - Tres columnas en escritorio para:
    - Catálogo
    - Carrito
    - Datos del cliente
  - Una sola columna en pantallas pequeñas mediante media queries.

- Paneles (`.panel`, `.panel-catalogo`, `.panel-carrito`, `.panel-cliente`):

  - Fondo claro, bordes redondeados, padding interno y sombra suave.
  - Títulos con tamaño y margen adecuados.

- Listas y elementos del carrito y catálogo:

  - `.lista`, `.item`, `.item-info`, `.item-nombre`, `.item-precio`, `.item-cantidad`.
  - Tarjetas blancas con separación y alineación flex.

- Botones:

  - Clase base `.btn` y variantes:
    - `.btn-agregar` (verde, para agregar al carrito).
    - `.btn-cantidad` (controles de cantidad).
    - `.btn-quitar` (rojo, para eliminar ítems).
    - `.btn-finalizar` (azul, botón principal de acción).
  - Estados `hover` y `disabled` para mejor experiencia de usuario.

- Formulario de cliente:

  - `.campo` para espaciado entre inputs.
  - Labels e inputs como elementos de bloque.
  - `width: 100%` y `box-sizing: border-box` para que se mantengan dentro del panel en todas las resoluciones.

- Mensajes:

  - `.carrito-vacio` para mensaje cuando no hay productos.
  - `.ayuda` para aviso de completar datos.
  - `.mensaje-exito` como bloque destacado en verde claro con el resumen de la compra.

## Despliegue

El proyecto se prepara para despliegue como sitio estático:

- Configuración de `base` en `vite.config.js` con el nombre del repositorio para que los recursos se carguen correctamente en GitHub Pages.
- Scripts en `package.json` para construir el proyecto (`npm run build`) y desplegarlo (`npm run deploy`) desde la carpeta `dist`.

## Aprendizajes

Este ejercicio consolida:

- Uso de `reactive`, `ref` y `computed` en Vue 3 con Composition API.
- Patrón de separación entre estado y vista.
- Manejo de listas y eventos en la interfaz con `v-for` y `@click`.
- Validación básica de formularios y control de estados de botones.
- Gestión de reactividad al modificar arrays y objetos.
- Proceso completo de despliegue de una SPA de Vue en GitHub Pages.

## Puedes ver el resultado en:

https://zakkdruzer.github.io/vue-ejerc-dual/
