# Renta multi-día — Prueba de concepto (V27-multidia, copia de V26)

## Diseño
- Campo opcional en el contrato: `fFin` (YYYY-MM-DD). Vacío = 1 día → los contratos existentes quedan idénticos (sin migración). `dias` se guarda como dato informativo; la fuente de verdad es `rentalDays(c)` (shared.js).
- **Precio**: artículos del catálogo = precio × cantidad × días. **Traslado = un solo monto** (se cobra una vez). Adicionales/Fairy NO se multiplican (decisión a confirmar).
- **Inventario**: se valida día por día; un contrato multi-día ocupa inventario todos sus días; la alerta indica el peor día.
- **Entregas**: entrega el día inicial, **recolección el día final** (fila "↩ RECOLECCIÓN", cuenta en ruta y progreso). Los totales del día solo cuentan entregas.
- `resolveStatus`: auto-cierre 5 días después de `fFin`.

## Contrato formal / documentos
- **PDF formal (móvil)**: "Periodo de renta (N días)" con rango completo, columna **Días**, precio **/día**, importe del periodo, "Subtotal artículos (N días)", "Traslado (cargo único)" y cláusulas 1 y 2 reescritas para el periodo.
- **A4 compartido (PDF/PNG/JPG en escritorio y móvil)**: fecha como "Periodo de renta", cantidad "10 × 3 d", importe por periodo y cláusulas de periodo. Ahora incluye también artículos adicionales/Fairy (antes no aparecían).
- **Word**: periodo en el párrafo de fecha y cantidad "N x D días". La plantilla fija no admite artículos adicionales (limitación previa).
- **Cotización (móvil)**: mismo campo "Hasta", cálculo, inventario y PDF; al convertirla en contrato conserva el rango.
- Calendario .ics: del primer al último día.

## Escritorio (index.html) — actualizado a paridad con móvil
- Campo "Hasta", días y total multi-día; artículos adicionales y Fairy; resta descuenta abonos (se registran desde móvil).
- El exceso de inventario ahora **bloquea el guardado** (antes solo avisaba).
- **Bug corregido (ya existía):** la tabla de artículos se construía una sola vez; con el catálogo dinámico (18 artículos vs 13 filas) fallaba "Nuevo/Editar". Ahora se reconstruye con `onCatalogChange`.
- Al guardar ya no se pierden `extraItems`/abonos de contratos creados en móvil (antes el total se recalculaba sin adicionales).
- Clientes, Reportes, Catálogo y Cotizaciones siguen abriéndose en la versión móvil (enlaces del menú).

## Despliegue (importante)
- Se subió `CACHE_VERSION` de `sw-app.js` (v4) y `sw-entregas.js` (v5): el service worker sirve `shared.js` desde caché (cache-first), sin esto los usuarios con la app instalada seguirían con la versión anterior.

## Pendiente / no cubierto
- `finanzas.html`: usa `fEvento` (ingreso en el mes de inicio); `Reportes` igual (no se prorratea entre meses).
- `Contrato-CASVEL.html` (deprecado): sin cambios.
- Verificar impresión real de Word y PDF con contratos de muchas líneas.
