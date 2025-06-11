# Guía de despliegue con GitHub Pages

El despliegue de aplicaciones y sitios web en la nube es una práctica fundamental en el desarrollo de software. En entornos educativos, utilizar GitHub Pages permite a docentes y estudiantes publicar proyectos de manera sencilla, asegurando accesibilidad, mantenibilidad y control de versiones.  

El término "deploy" se refiere al proceso de poner en funcionamiento un sistema o aplicación en un entorno donde pueda ser utilizado por los usuarios finales. 

[GitHub Pages](https://docs.github.com/es/pages) es un servicio de hosting basado en la nube que permite publicar sitios web estáticos directamente desde un repositorio de GitHub. Esto aprovecha los beneficios de cloud computing, como:

- **Accesibilidad Global**: Los sitios están disponibles desde cualquier ubicación con acceso a Internet.
- **Escalabilidad y Fiabilidad**: GitHub maneja la infraestructura sin necesidad de configuraciones adicionales.
- **Control de Versiones**: Al estar integrado con Git, permite rastrear cambios y colaborar de manera eficiente.

## Pasos

1. **Crear un repositorio** con el código fuente del proyecto. Asegurarse de que incluya un archivo `index.html`.
2. **Ir a la sección de _Settings_ del repositorio**. En la sección de _Pages_:
   - Cambiar la **visibilidad a público** para que el contenido sea accesible.
   - En la opción de **Build and deployment**, seleccionar **GitHub Actions** como _source_.
   - Seleccionar la opción _create your own_.
   ![](./assets/d69f1d127a7ff80bafa633dcfdbe23592179d33d.png)
3. **Sustituir el bloque de código por defecto** por el siguiente y nombrar el archivo como `deploy.yml` (**es importante incluir la extensión `.yml`**):

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

> **Aclaración: Es importante destacar que si se tiene la lógica de la interfaz y del dominio en un path diferente al brindado por el
> template del obligatorio (/src) se deberá cambiar en el código brindado el path en donde está indicado.**
>
> En la sección de branches se indica la rama sobre la cual se realizará
> el deploy (se mostrará el contenido que corresponda al código que se
> encuentre en esa rama). En este caso, se hará sobre la rama de **main,
> es decir, lo que se mostrará en el navegador una vez terminado el
> proceso corresponde al código en main.**
>
> ![](./assets/f4cccf6c4d71cbb385b9b0970259dddf748ad6dc.png)

En la sección de branches se indica la rama sobre la cual se realizará el deploy (se mostrará el contenido que corresponda al código que se encuentre en esa rama). En este caso, se hará sobre la rama de main, es decir, lo que se mostrará en el navegador una vez terminado el proceso corresponde al código en main. 

![workflow](./assets/f4cccf6c4d71cbb385b9b0970259dddf748ad6dc.png)

4.  Seleccionar **Commit Changes** para guardar el workflow. Si se hace sobre main recordar traerse ese cambio a las ramas donde sea necesario.

5.  Esperar a que se realice el deploy. Esto puede llevar algunos minutos.

Se puede ver el estado del deploy en el menú que aparece a la derecha de la sección **Code** (ver nota al pie del documento si este paso falla):

 ![](./assets/c235428cdef0762f52bb5b8473cdf2897c1af592.png)

> **Es importante considerar que muchas veces puede aparecer cómo realizado pero que puede tardar unos minutos en mostrar el contenido en la url.**

6.  Ingresar a la pestaña de Settings del repositorio. En la sección de Pages se podrá ver la URL donde la aplicación se encuentra
    hosteada.
    ![](./assets/811a75e965e6c2d038ed2d302caf0d15a10c403b.png)

7.  Acceder a la url, sumándole el path interfaces/index.html al final.
    En este ejemplo se deberá acceder a <https://ingsoft-fis-2025-1.github.io/prueba-deploy-pages-equipo1/interface/index.html>

## Fallas frecuentes

En caso de que falle se puede ir a la sección de Actions, seleccionar el workflow que falló:

![](./assets/06b754e5d3f3a8917c0b5aa571ec639af7a0778e.png)

Seleccionar Re-run all jobs:

![](./assets/697e6150d7aaef6ab9728ed7381987833fd4f572.png)

Si sigue fallando verificar que el bloque de código se copió correctamente (la identación es importante en los archivos de tipo
.yml), que la extensión del archivo creado sea .yml y si el path al cuál se apunta es el deseado.
