# Gym Tracker

App de entrenamiento en GitHub Pages con Google Sheets como base de datos. Todo se despliega con GitHub (Actions, Secrets y Pages).

- Ejercicios con series, repeticiones, segundos, peso y fecha
- Marca ejercicios hechos y el gráfico se rellena en directo
- Botón **Ordenar óptimo**: compuestos grandes → aislamiento → músculos pequeños → core → cardio, sin repetir músculo seguido
- Memoria muscular: series de los últimos 7 días y estado de recuperación (48 h)
- Copiar un día a otra fecha para repetir la rutina

## 1. Google Sheet

Crea una hoja de cálculo con una pestaña llamada exactamente `Ejercicios` y estas columnas en la fila 1, en este orden:

| Col | Encabezado | Contenido |
|-----|-----------|-----------|
| A | `id` | Identificador único (texto) |
| B | `fecha` | Día en formato `2026-10-06` (formato de columna: texto sin formato) |
| C | `orden` | Posición del ejercicio en el día (0, 1, 2…) |
| D | `ejercicio` | Nombre, por ejemplo `Press banca` |
| E | `musculo` | Pecho, Espalda, Hombros, Bíceps, Tríceps, Cuádriceps, Isquios, Glúteos, Gemelos, Core o Cardio |
| F | `series` | Número de series |
| G | `reps` | Repeticiones por serie |
| H | `segundos` | Tiempo de ejecución por serie, en segundos |
| I | `peso` | Kilos (0 si no aplica) |
| J | `hecho` | `TRUE` o `FALSE` |

Si no creas la pestaña, `Code.gs` la crea sola con estos encabezados. Las filas las escribe la app; no las edites a mano mientras la usas.

## 2. API (Apps Script)

1. En la hoja: **Extensiones > Apps Script**, pega `Code.gs`.
2. **Configuración del proyecto > Propiedades del script**: añade `TOKEN` con una clave larga y aleatoria.
3. **Implementar > Nueva implementación > Aplicación web**: ejecutar como *yo*, acceso *cualquier usuario*.
4. Copia la URL que termina en `/exec`.

Tras cambiar `Code.gs`: **Implementar > Gestionar implementaciones > Editar > Nueva versión**.

## 3. GitHub (repo, Secrets y Pages)

```bash
git init
git add .
git commit -m "Gym Tracker"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/gym-tracker.git
git push -u origin main
```

En **Settings > Secrets and variables > Actions > New repository secret** crea:

| Secret | Valor |
|--------|-------|
| `GYM_API_URL` | La URL `/exec` de Apps Script |
| `GYM_API_TOKEN` | El mismo `TOKEN` de las Propiedades del script |
| `GYM_PASSWORD` | La contraseña para abrir la app |

En **Settings > Pages > Source** elige **GitHub Actions** y lanza el workflow (push a `main` o *Run workflow*).

## Cómo queda protegido

El workflow inyecta la URL y el token en el HTML y luego lo cifra con [StatiCrypt](https://github.com/robinmoisson/staticrypt) usando `GYM_PASSWORD`. Lo que se publica es solo una pantalla de contraseña con el contenido cifrado: sin la contraseña no se ve ni el código, ni la URL, ni el token. Al entrar, la página se descifra en tu navegador y queda recordada 30 días en ese dispositivo.

Limitaciones: quien conozca la contraseña puede ver la URL y el token desde las herramientas del navegador, y una contraseña débil se puede forzar con ataques offline. Usa una contraseña larga.
