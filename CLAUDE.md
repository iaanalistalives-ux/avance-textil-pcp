# CLAUDE.md — Avance Textil PCP (avance-textil-pcp)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
Avance de hilado, tejeduría y tintorería de PCP: kilos requeridos contra kilos avanzados por grupo de producción, a partir del último corte publicado del reporte `rptplantexfechito`.
- `index.html` — **tablero** de solo lectura. Todo ponderado (suma de kg avanzados ÷ suma de kg requeridos, nunca promedio de porcentajes):
  - Tintorería = avance teñido ÷ requerido teñido (todas las líneas).
  - Tejeduría = avance tejido ÷ requerido tejido (solo líneas de servicio; se excluyen las de clase "compra").
  - Hilado = recibido ÷ comprado (solo grupos con hilado comprado; si Comprado = 0 se considera en stock).
  - Estado por línea según la fecha de compromiso del proveedor: Completo, Sin fecha, Vencido, Por vencer (≤ 7 días), En plazo; el del grupo es el peor de sus líneas. Twill se excluye por defecto.
  - KPIs: avance de tintorería (principal), de tejeduría, hilado recibido, kilos vencidos y pendientes, con variación contra el corte anterior (no hay meta).
  - Filtros de cliente, grupo, proveedor, estado, búsqueda y Twill. Pestañas Etapas, Atrasos, Hilado, Detalle por grupo, Evolución y Calidad del dato.
- `cargar.html` — **carga del corte** en 3 pasos: elegir el Excel, revisar totales y avisos (líneas sin fecha de compromiso, avance mayor al requerido, órdenes de hilado repetidas), publicar. Publicar dos veces con la misma fecha reemplaza el corte, no lo duplica.
  - Archivo: reporte `rptplantexfechito` (.xlsx, .xls o .xlsm); hoja "rpt" o la primera; la fila de encabezados se busca en las primeras 30 filas ("grupo" y "req teñido").
  - El avance de tintorería suma las tres columnas de avance teñido.

## Base de datos (Supabase lives-datos)
- `avtex_leer` (base, histórico y cortes) y `avtex_publicar` (con `avtex_puede_cargar` como aviso al abrir).
- Tablas: `avtex_base` (se reemplaza entera en cada carga), `avtex_historico` (resumen por grupo de cada corte) y `avtex_cortes`. El orden de las 26 columnas es un contrato entre la pantalla y las dos funciones.
- No usa Storage. Para eliminar un corte hay que pedirlo a Datos e IA (no hay función de borrado).

## Quién la usa
Según las entradas por el portal de los últimos 30 días al 21/10/2026: 1 persona (Analista de Planeamiento).

## Quién puede publicar
- Lo decide la base: `avtex_puede_cargar` (superusuario o quien tenga permiso a la carga en la tabla `permisos`). Sin permiso el tablero se ve y el botón Publicar se desactiva.
- A Sistemas: `avtex_puede_cargar` es la pieza que reemplazan con su seguridad; el reporte debería salir directo de sus sistemas.
