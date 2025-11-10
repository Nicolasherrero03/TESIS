# Archivo Vivo — Proyecto Web (Fusión 2D / 3D con Texto)

Descripción
-----------
Pequeño prototipo que mezcla una interfaz HTML (controles y parámetros) con una escena 3D realizada con Three.js.
La escena crea varias cajas con texto 3D (cada caja contiene un `TextGeometry` con el id) y las anima con velocidades aleatorias; además incluye controles de cámara (`OrbitControls`) y una función para aleatorizar posiciones usando GSAP.

Estructura de archivos
----------------------
- `index.html` — Página principal; contiene el HTML, estilos y la mayor parte del código JS de la app (escena, carga de fuente, generación de cajas, animación y eventos).
- `three.min.js` — Biblioteca Three.js (versión minificada incluida localmente).
- `OrbitControls.js` — Controlador de cámara (archivo clásico de Three.js incluido localmente).
- `gsap.min.js` — GSAP para animaciones (usado para la transición al randomizar posiciones).
- `FontLoader.js` — Atención: el fichero en el repositorio contiene código de un `AWDLoader` (loader para archivos .awd), no la típica `FontLoader` de Three. Revisar nombre/contenido.
- `Text.Geometry.js` — Código para geometrias paramétricas y `ParametricGeometries` (se usa un `TextGeometry` en `index.html`).
- `helvetiker_regular.typeface.json` — Fuente en formato JSON usada por `THREE.FontLoader` para crear `TextGeometry`.

Requisitos
---------
- Navegador moderno con WebGL habilitado (Chrome, Firefox, Edge).
- Recomiendo servir los archivos desde un servidor HTTP local. Algunos navegadores bloquean la carga del archivo de fuente (`.json`) usando `file://`. Opciones:

  - Si tienes Python 3 instalado (PowerShell):

    ```powershell
    python -m http.server 8000
    ```

    y abrir en el navegador: http://localhost:8000/

  - Usar la extensión "Live Server" de VSCode (rápido y fácil).
  - O instalar `http-server` con Node: `npx http-server`.

Cómo ejecutar
-------------
1. Asegúrate de servir la carpeta del proyecto por HTTP (ver Requisitos).
2. Abre `http://localhost:8000/index.html` (o la URL que te devuelva tu servidor).
3. Usa el panel derecho para mover sliders y el botón "Randomizar" para activar la animación de posiciones.

Observaciones y revisión de código (puntos importantes)
-----------------------------------------------------
1. Inconsistencia entre nombres de archivos y contenido:
   - `FontLoader.js` parece contener un `AWDLoader` (carga de archivos .awd). En `index.html` se utiliza `new THREE.FontLoader()` y se carga `helvetiker_regular.typeface.json`. Si dependes de una `FontLoader` separada, asegúrate de que el archivo `FontLoader.js` realmente exporte/declare `THREE.FontLoader` o incluye `FontLoader` oficial de Three.js.
   - En el `head` de `index.html` se referencia `TextGeometry.js` (sin punto), pero el archivo en el repositorio es `Text.Geometry.js` (con un punto). Revisa nombres y rutas de `script`.

2. HTML: etiquetas duplicadas
   - `index.html` contiene dos cierres `</body></html>` duplicados al final; elimina la repetición para mantener HTML válido.

3. Carga de la fuente
   - El código usa `THREE.FontLoader().load('./helvetiker_regular.typeface.json', ...)` — correcto si la fuente está disponible por HTTP. Si usas `file://`, la mayoría de navegadores bloquearán la petición.
   - Buena práctica: en el `onError` de carga de fuente mostrar un UI/alert para el usuario o fallback visual.

4. Canvas/resolución
   - Se usa `renderer.setSize(container.clientWidth, container.clientHeight)` y se actualiza en `resize` — bien.
   - Recomendación: añadir `renderer.setPixelRatio(window.devicePixelRatio)` al inicializar el renderer para pantallas HiDPI.

5. Rendimiento y limpieza
   - Separar librerías de terceros (Three, GSAP, OrbitControls) en una carpeta `lib/` o `vendors/` y mantener tu código en `src/` para claridad.
   - Considerar usar versiones no-minificadas durante desarrollo y minificadas en producción.
   - Si la escena crece, pasar a `BufferGeometry` y optimizar materiales (evitar `MeshBasicMaterial` con muchos objetos si necesitas iluminación).

6. Robustez del `animate()`
   - Actualmente `animate()` siempre itera `boxesGroup.children.forEach(...)`. Si `boxesGroup` está vacío, no pasa nada. Aún así, podrías proteger con `if (boxesGroup.children.length > 0) { ... }` para claridad.

7. Controles
   - `controls.enableDamping = true` está activado; recuerda que `controls.update()` ya se llama dentro del bucle de animación — correcto.

Pequeñas mejoras recomendadas (prioritarias)
-----------------------------------------
- Corregir nombre/contenido de `FontLoader.js` o reemplazarlo por la versión oficial de Three.js para `FontLoader`.
- Arreglar la referencia a `TextGeometry` y/o renombrar el archivo a `TextGeometry.js` para que coincida con el `script src`.
- Eliminar cierre HTML duplicado.
- Añadir `renderer.setPixelRatio(window.devicePixelRatio);` tras crear el renderer.
- Añadir manejo de errores visible si la fuente no se carga (informe al usuario en la UI).

Mejoras a mediano plazo
-----------------------
- Modularizar el código: mover la lógica de escena a `src/app.js` o similar y dejar `index.html` ligero.
- Considerar exportar/usar modelos GLTF para textos complejos o para portar assets pesados.
- Añadir una pequeña suite de tests (unitarios) para funciones utilitarias si se amplía el proyecto.

Licencia y autor
----------------
Autor: [Tu Nombre] — reemplaza este texto con tu nombre.
Licencia: (No especificada). Añade una licencia (MIT, CC BY-SA, etc.) si quieres permitir la reutilización.

Notas finales
------------
He creado esta revisión desde los archivos en el proyecto. Si quieres, puedo:

- Corregir automáticamente las rutas y el cierre duplicado en `index.html` y generar una versión limpia. (Puedo hacerlo ahora.)
- Reemplazar `FontLoader.js` por la versión oficial si me das permiso para editar.
- Añadir un pequeño `server.js` o `package.json` con script `start` para lanzar un servidor local simple.

— Fin del README —
