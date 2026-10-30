# Ambiente de pruebas CASVEL (Firebase aparte)

## Por qué un proyecto aparte y no `casvel_catalog_dev`
La app usa 5 nodos (`casvel_v1`, `casvel_catalog`, `casvel_entregas`, `casvel_finanzas`, `casvel_inventory_v1`) y su config de Firebase se guarda en el navegador (`localStorage["casvel_fb_cfg"]`), no en el código. Un proyecto separado = **cero cambios al código** y producción queda físicamente inaccesible desde pruebas.

## Importante: no abrir los archivos con doble clic (file://)
Todos los `file://` comparten el mismo localStorage: la config de Firebase de pruebas pisaría la de producción y mezclaría contratos locales. Usa servidores locales con puertos distintos (cada puerto = su propio localStorage):

- Producción V26 → http://localhost:8026  (config `casvel-prod-V26` en `.claude/launch.json`)
- Pruebas V27   → http://localhost:8027  (config `casvel-dev-V27`)

Manual: `py -3 -m http.server 8027 --directory V27-multidia`

## Pasos (consola de Firebase, los haces tú)
1. Firebase Console → **Agregar proyecto** → nombre p. ej. `casvel-dev` (plan Spark, gratis; sin Analytics).
2. **Compilación → Realtime Database → Crear base de datos** (modo bloqueado).
3. Pestaña **Reglas** → pega el contenido de `firebase-rules.json` → Publicar.
4. **Compilación → Authentication → Comenzar → Anónimo → Habilitar** (la app usa `signInAnonymously`).
5. Configuración del proyecto → *Tus apps* → agrega app Web → copia `apiKey`, `databaseURL`, `projectId`.
6. Abre http://localhost:8027, botón de Firebase en la app, pega esos 3 datos.
7. (Datos) Exporta un respaldo de producción (`respaldos.html` o Realtime Database → ⋮ → Exportar JSON) e impórtalo en `casvel-dev` (⋮ → Importar JSON).

## Seguridad de la copia de producción
Contiene datos reales de clientes (nombre, teléfono, dirección). Alternativas: anonimizar el JSON antes de importar, o importar solo `casvel_catalog` y generar contratos ficticios. Si quieres, preparo un script que anonimice el respaldo.

## Recomendación extra
Que la pestaña de pruebas se distinga: título/banner "PRUEBAS" visible cuando `databaseURL` contenga `-dev`, para no confundirse de ambiente. (Pendiente si lo apruebas.)
