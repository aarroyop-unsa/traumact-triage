# Despliegue: GitHub → Streamlit Community Cloud

## 0. Preparar la carpeta
1. Descomprimir `TraumaCT-Triage_prototipo_streamlit.zip`.
2. Copiar en `weights/` los tres archivos `model_soft_tissue.pth`, `model_liver.pth`, `model_abdomen.pth`
   (sin ellos la app publicada funcionará solo en modo demostración).
3. Probar en local:
   ```bash
   python -m venv .venv
   .venv\Scripts\activate            # Windows   (macOS/Linux: source .venv/bin/activate)
   pip install -r requirements-local.txt
   python -m pytest -q
   streamlit run app.py
   ```

## 1. Crear el repositorio en GitHub
1. https://github.com/new → nombre `traumact-triage`.
2. Visibilidad: **Private** si los pesos no deben ser públicos (revisar las reglas de uso del dataset RSNA 2023);
   **Public** en caso contrario.
3. No marcar "Add a README" (la carpeta ya trae uno) → **Create repository**.

## 2. Subir el código
Con Git instalado (https://git-scm.com), dentro de la carpeta `traumact_triage`:
```bash
git init
git add .
git status                      # verificar que aparecen weights/*.pth y .streamlit/config.toml
git commit -m "Prototipo TraumaCT-Triage"
git branch -M main
git remote add origin https://github.com/USUARIO/traumact-triage.git
git push -u origin main
```
GitHub pedirá autenticación: usar el inicio de sesión del navegador o un *Personal Access Token*
(Settings → Developer settings → Personal access tokens) en lugar de la contraseña.

Alternativa sin Git: en el repositorio vacío, *uploading an existing file* → arrastrar el **contenido** de la carpeta
(máximo 25 MB por archivo y 100 archivos por carga desde el navegador). Comprobar que se subió `.streamlit/config.toml`;
si no, crearlo con *Add file → Create new file* escribiendo `.streamlit/config.toml` como nombre.

## 3. Conectar Streamlit Community Cloud
1. https://share.streamlit.io → **Continue with GitHub** y autorizar. Para repositorios privados, conceder acceso
   cuando lo solicite.
2. **Create app** → *Deploy a public app from GitHub* (o la opción equivalente para una app existente).
3. Repository: `USUARIO/traumact-triage` · Branch: `main` · Main file path: `app.py`.
4. App URL (opcional): por ejemplo `traumact-triage`.
5. **Advanced settings** → Python **3.12** → Save.
6. **Deploy**. La primera instalación (PyTorch CPU) tarda varios minutos; el registro aparece en el panel derecho.

## 4. Verificar
- Barra lateral: "Pesos disponibles para las 3 ventanas" → modo real; si no, modo demostración.
- Cargar estudios de demostración → Analizar y priorizar.
- Probar la carga de `ejemplos/estudios_sinteticos_dicom.zip`.

## 5. Actualizar
Cada `git push` a `main` redespliega la app automáticamente. Si cambia `requirements.txt`, la reinstalación tarda unos minutos.

## Problemas frecuentes
| Síntoma en el registro | Solución |
|---|---|
| `No matching distribution found for torch==2.5.1+cpu` | Confirmar Python 3.12 en Advanced settings (Settings → General si la app ya existe) y redesplegar |
| La app se reinicia o muestra "Oh no" al analizar | Falta de memoria: subir "Analizar 1 de cada N cortes" a 4–6 y cargar menos estudios a la vez |
| `FileNotFoundError` de pesos | Los `.pth` no se subieron a `weights/` o tienen otro nombre |
| `ModuleNotFoundError: cv2` | Debe usarse `opencv-python-headless` (ya incluido), no `opencv-python` |

## Reglas
- No subir estudios reales (ni del conjunto RSNA, ni de hospitales) al repositorio ni a la app pública.
- Mantener el aviso de que el resultado es de apoyo y la decisión corresponde al médico radiólogo.
