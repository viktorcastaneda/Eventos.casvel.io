# Renta multi-día — Prueba de concepto (V27-multidia, copia de V26)

## Diseño
- Campo opcional nuevo en el contrato: `fFin` (YYYY-MM-DD). Vacío = 1 día → **todos los contratos existentes quedan idénticos** (sin migración).
- `dias` se guarda como dato informativo; la fuente de verdad es `rentalDays(c)` (shared.js).
- **Precio**: artículos del catálogo = precio × cantidad × días. **Flete (`traslado`) = un solo monto**, no se multiplica. Artículos adicionales/Fairy NO se multiplican (decisión a confirmar).
- **Inventario**: se valida día por día en el rango; se reporta el peor día por recurso. Los contratos de varios días ocupan inventario todos sus días.
- **Entregas**: entrega el día inicial, **recolección el día final** (etiqueta ↩ RECOLECCIÓN). Totales del día solo cuentan entregas (no duplica).

## Archivos modificados (comentarios `ADDED` / `MODIFIED`)
- `shared.js`: helpers `rentalEnd/rentalDays/contractCoversDate/rangeDates/fmtRange`; `checkInventory(items,fEvento,excludeId,fFin)`; ICS (DTEND = fFin); A4 y DOCX (importe × días, "Periodo de renta").
- `mobile.html`: campo "Hasta", `mDatesChanged()`, `mCalc`, `mCollect/mPopulate/startNew`, hojas de inventario, resumen, tarjetas, detalle, Entregas.

## Pendiente / no tocado en la PoC (impacto)
- `index.html` (escritorio): sin campo fFin; sigue guardando 1 día (y al editar un contrato multi-día desde escritorio podría perder fFin → conviene añadirlo o bloquear edición).
- `entregas.html` (vista restringida de choferes) y `finanzas.html`: siguen usando `fEvento===fecha`; un contrato multi-día solo aparece el día inicial.
- Reportes (Por Año/Mes/Futuros): el ingreso se atribuye al mes de `fEvento` (contrato que cruza de mes no se prorratea).
- Cotizaciones (`q-*`): sin fFin aún; validación de inventario de cotizaciones es de 1 día.
- Contrato-CASVEL.html (deprecado): sin cambios.
- Plantilla DOCX: el texto de periodo cabe en el mismo párrafo; verificar impresión real.
- Auto-cierre `resolveStatus`: YA corregido (usa `rentalEnd`).
