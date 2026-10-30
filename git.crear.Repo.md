# Crear Repositorio

Este es un tutorial para crear un repositorio desde linea de comando
1. Entrar a la ruta del proyecto. `cd ruta/de/proyecto`
1. Inicializar git. `git init`
1. Agregar los Archivos.`git add .`
1. Crear el primer commit. `git commit -m "Mensaje commit"`
1. Crear el repositorio en GitHub Privado (requiere GitHub CLI (`gh`) instalado). `gh repo create nombre-del-repo --private --source=. --remote=origin --push
`
1. Crear el repositorio en GitHub Publico (requiere GitHub CLI (`gh`) instalado). `gh repo create nombre-del-repo --public --source=. --remote=origin --push`

<br />
**Si no tienes instalado GitHub CLI.**
1. Crea el repositorio vacio en GitHub y despues ejecutar:

```bash
git remote add origin https://github.com/TU_USUARIO/nombre-del-repo.git
git branch -M main
git push -u origin main
```
2. Comprobar que fue correcto con: 

```bash 
git remote -v
```

# Remove File from Repo

## Eliminarlo del repo y de tu carpeta local
```bash 
git rm ruta/al/archivo
git commit -m "Eliminar archivo"
git push
```

## Eliminarlo del repo pero conservarlo localmente
```bash 
git rm --cached ruta/al/archivo
git commit -m "Dejar de trackear archivo"
git push
```

## Eliminar un Archivo e Historial del Repositorio.
Hay que realizar varios pasos para este proceso. La forma recomendada actualmente es git filter-repo.
Sí. Si quieres **eliminar completamente un archivo del historial de Git**, no basta con `git rm`: hay que reescribir los commits anteriores.

 La forma recomendada actualmente es `git filter-repo`.

 ### 1\. Haz una copia del repositorio

 Como vas a reescribir el historial:

```
git clone --mirror <URL_DEL_REPO>
cd <REPO>.git
```

 ### 2\. Elimina el archivo de todos los commits

 Por ejemplo, si el archivo era:

```
config/secrets.json
```

 ejecuta:

```
git filter-repo --path config/secrets.json --invert-paths
```

 Esto elimina `config/secrets.json` de **todos los commits del historial**.

 Si quieres eliminar varios archivos:

```
git filter-repo \
  --path config/secrets.json \
  --path .env \
  --invert-paths
```

 ### 3\. Verifica que desapareció

 Puedes buscarlo en el historial:

```
git log --all -- config/secrets.json
```

 Idealmente no debería mostrar commits.

 También puedes comprobar:

```
git rev-list --objects --all | grep "config/secrets.json"
```

 ### 4\. Subir el historial reescrito

 Si estás trabajando con un repositorio remoto:

```
git push --force --mirror
```

 ⚠️ **Esto es destructivo:** cambia los hashes de los commits y puede afectar a otras personas que tengan clones del repositorio.

 Si el archivo contenía un **password, API key, token, secreto, etc.**, hay otro paso fundamental: **revoca/rota ese secreto**. Aunque lo elimines del historial, alguien pudo haberlo obtenido mientras estuvo publicado.

 ### Si ya tienes el repositorio local

 También puedes hacerlo directamente desde tu repositorio existente:

```
git filter-repo --path config/secrets.json --invert-paths
git push --force --all
git push --force --tags
```

 Pero antes de hacerlo, conviene saber **si el repositorio es compartido y si otras personas ya hicieron `clone`/`pull`**, porque el procedimiento para no causar problemas al equipo cambia un poco.

 Si me dices **qué archivo quieres eliminar y si está en GitHub/GitLab/Bitbucket**, te puedo dar los comandos exactos para tu caso.

---

# Agregar gitignore


La mejor forma de hacer esto es creando un archivo llamado `.gitignore` en la raíz de tu proyecto. Tienes dos opciones dependiendo de si quieres hacerlo manualmente o de forma automática.

**Opción 1: Forma automática (Recomendada)**
.NET tiene un comando integrado que genera un archivo `.gitignore` completo y optimizado para Visual Studio y .NET, el cual ya incluye las carpetas `bin` y `obj`, así como otros archivos temporales que no deben subirse al repositorio.

Ejecuta este comando en la raíz de tu proyecto:

`dotnet new gitignore`

**Opción 2: Forma manual**
Si prefieres crear el archivo tú mismo o ya tienes uno y solo quieres añadir esas carpetas, sigue estos pasos:

1. Crea el archivo en la raíz del proyecto:
   `touch .gitignore`

2. Abre el archivo con tu editor favorito y añade las siguientes líneas:

```text
bin/
obj/
```

**Para verificar que funciona:**
Si ya habías hecho un `git add .` antes de crear el `.gitignore`, es posible que Git ya esté rastreando esas carpetas. Para dejar de rastrearlas sin borrarlas de tu computadora, ejecuta:

`git rm -r --cached bin/ obj/`

Después de esto, haz un commit para guardar los cambios:

`git add .gitignore`
`git commit -m "Añadir gitignore para omitir bin y obj"`

# Hacer Merge en CLI

Para realizar un merge de la rama `dev` a la rama `master` utilizando la terminal, debes seguir estos pasos en orden:

1. **Cambia a la rama master**
   Primero debes posicionarte en la rama que recibirá los cambios (en este caso, `master`).
   `git checkout master`

2. **Actualiza tu rama master**
   Es fundamental asegurarse de que tienes la versión más reciente del servidor para evitar conflictos antiguos.
   `git pull origin master`

3. **Ejecuta el merge**
   Ahora trae los cambios de la rama `dev` hacia la rama donde estás parado (`master`).
   `git merge dev`

4. **Resuelve conflictos (Solo si aparecen)**
   Si Git te indica que hay "Merge Conflicts", deberás abrir los archivos marcados, elegir qué código conservar, guardar los archivos y ejecutar:
   `git add .`
   `git commit -m "Resueltos conflictos de merge"`

5. **Sube los cambios al servidor**
   Hasta ahora el merge solo ha ocurrido en tu computadora local. Para que el cambio se refleje en el repositorio remoto, debes hacer el push:
   `git push origin master`

**Resumen rápido de comandos:**
`git checkout master` $\rightarrow$ `git pull origin master` $\rightarrow$ `git merge dev` $\rightarrow$ `git push origin master`

--- 
---
# Como hacer git cherry pick 

Para aplicar un commit específico de otra rama a la rama en la que estás trabajando:

```bash
git switch mi-rama
git cherry-pick <hash-del-commit>
```

Por ejemplo:

```bash
git switch feature
git cherry-pick a1b2c3d
```

El hash lo puedes obtener con:

```bash
git log --oneline
```

Si hay conflictos, Git pausará el proceso. Resuélvelos en los archivos y luego ejecuta:

```bash
git add <archivo-resuelto>
git cherry-pick --continue
```

Para cancelar el cherry-pick y volver al estado anterior:

```bash
git cherry-pick --abort
```

Para aplicar varios commits consecutivos, usa el rango:

```bash
git cherry-pick <hash-inicial>^..<hash-final>
```

Ese rango incluye ambos commits.⚖️

# Como Editar mensaje de commit.

Depende de si el commit es el más reciente o uno anterior.

- **Editar el último commit** (mensaje y, si quieres, cambios preparados):

```bash
git commit --amend
```

Se abrirá el editor configurado en Git. Guarda y cierra para confirmar el nuevo mensaje. Para escribirlo directamente:

```bash
git commit --amend -m "Nuevo mensaje del commit"
```

- **Editar un commit anterior:** inicia un rebase interactivo con el número de commits que quieras revisar:

```bash
git rebase -i HEAD~3
```

En el editor, cambia `pick` por `reword` junto al commit cuyo mensaje quieres modificar. Guarda y cierra; Git te pedirá el mensaje nuevo.

Si el commit ya fue enviado al repositorio remoto, cambiarlo reescribe el historial. Para actualizar la rama remota, normalmente se usa:

```bash
git push --force-with-lease
```

Hazlo con cuidado si otras personas comparten esa rama; coordina antes para no sobrescribir cambios ajenos.


Last Edit 

Para cambiar el mensaje de un commit que **no es el último**, usa un *rebase interactivo*. Esto modifica el historial de la rama.

1. **Identifica el commit** y cuántos commits recientes hay desde él:

```bash
git log --oneline
```

Por ejemplo:

```text
f4a1b20 (HEAD -> mi-rama) Agrega pruebas
c8d2e31 Corrige validación
a7b9c42 Añade endpoint
```

Si quieres cambiar `c8d2e31`, que está a dos commits de `HEAD`, inicia un rebase que incluya esos tres commits:

```bash
git rebase -i HEAD~3
```

2. **Marca el commit para editar su mensaje.** Se abrirá una lista parecida a esta:

```text
pick a7b9c42 Añade endpoint
pick c8d2e31 Corrige validación
pick f4a1b20 Agrega pruebas
```

Cambia `pick` por `reword` —o su abreviatura `r`— en la línea del commit deseado:

```text
pick a7b9c42 Añade endpoint
reword c8d2e31 Corrige validación
pick f4a1b20 Agrega pruebas
```

Guarda y cierra el archivo.

3. **Escribe el nuevo mensaje.** Git abrirá el editor otra vez para el commit marcado. Reemplaza el mensaje, guarda y cierra. Si todo sale bien, el rebase terminará y los commits posteriores se conservarán, pero tendrán nuevos hashes porque se recrearon.

Si aparece un conflicto, resuélvelo y continúa:

```bash
git add <archivos-resueltos>
git rebase --continue
```

Para cancelar el proceso:

```bash
git rebase --abort
```

Si el commit ya estaba publicado en el remoto, tendrás que actualizar la rama con:

```bash
git push --force-with-lease
```

Como esto reescribe el historial, evita hacerlo en una rama compartida sin coordinarlo con quienes la usan.



