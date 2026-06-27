# Por Amor Al Mate — Sistema de Gestión

> Archivo ancla del proyecto. Léelo al inicio de cada sesión para recuperar el contexto.
> Última actualización: 2026-06-27

## Qué es

App web de **una sola página** para gestionar el emprendimiento "Por Amor Al Mate"
(venta de mates, bombillas, termos, yerba, accesorios y servicios como grabado láser).
Todo vive en un único archivo: **`index.html`** (~3300 líneas).

El dueño/usuario es **Javier** (javieramado91@gmail.com). Escribe en español (Argentina).
Responder siempre en español. No es programador: explicar simple, evitar tecnicismos.

## Stack técnico

- **React 18** vía Babel standalone + importmap (esm.sh) — sin build, sin npm. Se abre el `.html` directo.
- **Firebase Firestore** como base de datos (tiempo real con `onSnapshot`).
- **Tailwind CSS** vía CDN para los estilos.
- **Lucide React** para íconos + **íconos SVG inline propios** (mates, bombilla, termo, láser, etc.).
- Todo el estado se maneja con `useState` / `useMemo` dentro de un componente grande.

### Colecciones de Firestore
Ruta base: `artifacts/{appId}/public/data/{coleccion}`
- `orders` — pedidos
- `inventory` — inventario de productos
- `debts` — deudas
- `expenses` — gastos
- `extra_incomes` — ingresos extra (no provenientes de pedidos)
- `internal_transfers` — movimientos internos (caja efectivo → cuenta bancaria, sin impacto en ingreso/gasto)

## Estructura de la app (pestañas / tabs)

`activeTab`: `orders` (Pedidos), `inventory` (Inventario), `debts` (Deudas), `finances` (Finanzas), `stats` (Estadísticas).
Login simple con usuario/contraseña al entrar. Sidebar oscuro a la izquierda en desktop.

## Funcionalidades implementadas (historial de trabajo)

### Pedidos
- Carrito con selector **Producto** o **Servicio** (toggle). Servicios sugeridos como chips (Grabado láser preselizado).
- En el dropdown de productos se muestra **el precio**.
- Se guardan con **fecha + hora exacta**; la hora se muestra en las tarjetas de pedido.
- **Modal de pago** rediseñado: total/saldo/cobrado claros, barra de progreso de seña con badges de método (efectivo/transferencia), distingue pago total / mixto / seña.
- **Eliminar pedido** con opción de **reponer stock** al inventario (usa `writeBatch` atómico).
- Tarjetas resumen modernas (barra de progreso, montos pendientes, conteo de importantes).
- Íconos SVG propios y representativos por tipo de producto (`PRODUCT_ICON_MAP` resuelve por palabras clave del nombre).

### Finanzas
- Botón de **movimiento interno** (mover dinero de caja efectivo a cuenta bancaria sin generar ingreso ni gasto).
- **Gastos e ingresos se guardan con fecha + hora actual real** (no hora fija), para que los movimientos se ordenen bien.
- **Filtros de movimientos**: por tipo (ingreso / gasto / movimiento interno), por método (efectivo / transferencia) y orden por fecha (asc/desc).
- `finances` (useMemo) calcula saldos de caja efectivo y cuenta bancaria, restando/sumando transferencias internas.

### Estadísticas
- Sección completa: alertas de pendientes, top clientes, top productos, tendencia 6 meses, hora pico, tasa de conversión, ganancia neta.
- Layout de tarjetas blancas limpias (se eliminó un bloque oscuro que no gustaba).
- Gráfico mensual **agrupa los días en semanas** (no 30 barritas).

### DailyVibe (frase + clima)
- 50 frases sobre el mate con anti-repetición (sessionStorage).
- Gradiente según clima, tips según clima, botón de refrescar.
- Clima vía open-meteo API; búsqueda de ubicación vía geocoding API.

### Branding
- **Logo**: archivo `logo.jpg` (texto blanco "POR AMOR AL MATE" + mate sobre fondo negro).
  - En el **sidebar**: se muestra grande (≈200px alto) con `mix-blend-screen` para que el fondo negro desaparezca y solo flote la tipografía blanca sobre el sidebar oscuro. Debajo, el indicador "En línea".
  - En el **login**: imagen completa en tarjeta blanca con borde.
- **Favicon**: ícono SVG de un mate (calabaza marrón + bombilla + fondo verde), embebido como data URI en el `<head>`.

## Convenciones y preferencias del usuario

- **Estética**: le importa que se vea moderno, elegante y "que no se note" forzado. Itera mucho sobre detalles visuales (tamaño de logo, recortes, etc.). Mostrar cambios concretos y pedir feedback.
- **Idioma**: siempre español argentino.
- **Fecha y hora**: insiste en que TODO movimiento (pedido, pago, gasto, ingreso) se guarde con fecha y hora para poder ordenar cronológicamente. Mantener este patrón en cualquier cosa nueva.
- Convertir fechas relativas a absolutas al guardar contexto.

## GitHub

- Repo: **https://github.com/javieramado91-dotcom/PorAmorAlMate**
- Rama principal: `main`. Identidad configurada localmente (javieramado91 / javieramado91@gmail.com).
- `gh` CLI NO está instalado; se usa `git` por HTTPS (la autenticación ya funciona, hay credential helper).
- Para subir cambios: `git add -A && git commit -m "..." && git push`
- Firma de commits hechos por Claude: `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`

## Cómo probar

Abrir `index.html` directamente en el navegador (no requiere servidor ni build).

## Notas para futuras sesiones

- Antes de tocar algo, ubicarlo con Grep en `index.html` (archivo gigante de un solo bloque).
- Al agregar guardado de datos nuevos, incluir SIEMPRE fecha + hora.
- Vigilar el contador de tokens: avisar al usuario cuando queden ~40k y actualizar este archivo antes de cerrar.
