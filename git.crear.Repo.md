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