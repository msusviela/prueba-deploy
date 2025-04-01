# Guía de despliegue con GitHub Pages

El despliegue de aplicaciones y sitios web en la nube es una práctica fundamental en el desarrollo de software. En entornos educativos, utilizar GitHub Pages permite a docentes y estudiantes publicar proyectos de manera sencilla, asegurando accesibilidad, mantenibilidad y control de versiones.

GitHub Pages es un servicio de hosting basado en la nube que permite publicar sitios web estáticos directamente desde un repositorio de GitHub. Esto aprovecha los beneficios de cloud computing, como:

- **Accesibilidad Global**: Los sitios están disponibles desde cualquier ubicación con acceso a Internet.
- **Escalabilidad y Fiabilidad**: GitHub maneja la infraestructura sin necesidad de configuraciones adicionales.
- **Control de Versiones**: Al estar integrado con Git, permite rastrear cambios y colaborar de manera eficiente.

## Requisitos

- Un repositorio creado en la organización del dictado.
- Un proyecto creado en ese repositorio que cuente con el `index.html`.

## Motivación

- Presentar la **disponibilidad** como atributo de calidad.
- Ilustrar de forma clara las **estrategias de branching** (por ejemplo, al desplegar el código en `main`, los alumnos podrán entender mejor la diferencia entre `main` y `develop`).
- Introducción al concepto de **deploy** y su vínculo con el proceso de desarrollo de software, en particular con la etapa de **mantenimiento**.
- Facilita una **nueva etapa de validación** con interesados.
- Testing en un **ambiente independiente** a la configuración local del alumno.
- Posibilidad de que el estudiante pueda **mostrar el trabajo realizado** a lo largo del semestre.
- Oportunidad para que los alumnos desplieguen **código estático** y generen un **portfolio**.

## Pasos

1. **Crear un repositorio** con el código fuente del proyecto. Asegurarse de que incluya un archivo `index.html`.
2. **Ir a la sección de _Settings_ del repositorio**. En la sección de _Pages_:
   - Cambiar la **visibilidad a público** para que el contenido sea accesible.
   - En la opción de **Build and deployment**, seleccionar **GitHub Actions** como _source_.
   - Seleccionar la opción _create your own_.
3. **Sustituir el bloque de código por defecto** por el siguiente y nombrar el archivo como `deploy.yml` (es importante incluir la extensión `.yml`):

```yaml
name: Deploy static content to Pages

on:
  # Se ejecuta al hacer push a la rama predeterminada (main)
  push:
    branches: ["main"]

  # Permite ejecutar el flujo de trabajo manualmente desde la pestaña de Actions
  workflow_dispatch:

# Configura los permisos del GITHUB_TOKEN para permitir el despliegue a GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4  
      - name: Setup Pages
        uses: actions/configure-pages@v5   
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3 
        with:
          # Path
          path: './src'  
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

> **Nota**: Si la estructura del proyecto no sigue el directorio `/src`, modificar el _path_ en el código.

4. **Seleccionar _Commit Changes_** para guardar el workflow. Si se hace sobre `main`, recordar traer ese cambio a otras ramas si es necesario.
5. **Esperar a que se realice el deploy**. Puede tomar algunos minutos.
6. **Verificar el estado del deploy** en el menú derecho de la sección _Code_.
7. **Acceder a la URL** desde la pestaña _Settings_ > _Pages_.
   - La URL seguirá la estructura: `https://<usuario>.github.io/<repositorio>/interface/index.html`.

## Solución de problemas

Si el deploy falla:

- Ir a la sección de **Actions**.
- Seleccionar el workflow que falló y hacer clic en **Re-run all jobs**.
- Verificar que el bloque de código se copió correctamente (la indentación en YAML es crítica).
- Asegurar que el **path** al que apunta el deploy es el correcto.

---

