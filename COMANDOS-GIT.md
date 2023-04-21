# COMANDOS GIT

## git branch

Muestra todas las ramas del repositorio local y señala en la que se encuentra

### git branch -a

Lista todas las ramas del repositorio, tanto local como remotas.  

### git branch <'rama'>

Este comando crea una rama a partir de la rama en la que estes situado.  

`*Nota: Para borrar una rama sera necesario estar en otra ajena a esa rama recomendado hacerlo desde la rama MASTER.`  

### git checkout -b <'rama'>

Este comando crea una rama a partir de la rama en la que estes situado y se mueve a la nueva rama.  

`*Nota: Se puede lanzar exepcion si hay cambios en el area de trabajo o area de pruebas.`  

### git branch -d <'rama'>

Borra la rama especificada y todos los archivos.  

`*Nota: Puede lanzar una alerta en caso de que contenga cambios no MERGE con la rama que intenta borrarla.`  

### git branch -D <'rama'>

Borra la rama especificada y todos los archivos.  

`*Nota: Forza a borrar la rama independiente de que existan cambios sin MERGE`  

### git push --set-upstream origin <'rama'>

Necesario cuando se cree una nueva rama local puesto que no tiene establecido un origen en el remoto.

## git status

Muestra un resumen de la rama y los cambios pendientes en ella sin commit.  

## git add

Comando para agregar los cambios al area de trabajo.  

`*Nota: puede ser utilizado archivo por archivo o con un punto al final para agregar TODOS los cambios del area de trabajo al area de ensayo.`  

## git reset

Deshace los cambios realizados en una determinadad rama  

### --Soft

Deshace los commits posteriores al commit que quieres regresar, pero los cambios con respecto al que regresas seran situados en el area de pruebas.  

### --Mixed

Deshace los commits posteriores al commit que quieres regresar, pero los cambios con respecto al que regresas seran situados en el area de trabajo sumando a los que puedas tener.  

### --Hard

Deshace los commits posteriores al commit que quieres regresar, ademas de no regresar ninguno de los cambios a ningun area de trabajo o pruebas.  

`*Nota: Por mucho el reset mas peligroso puesto que no se cuenta con ningun tipo de respaldo, al regresar a un commit se       perdera todo hasta el commit al que estas regresando.`  
  
### git reset HEAD

Regresa los cambios del area de ensayo al area de trabajo.  

`*Nota: puede ser utilizado tal cual para regresar TODOS los cambios al area de trabajo o uno a uno especificando el archivo`  

### git reset --[Tipo] [SHA-1]

Nos permitira regresar a un determinado commit y los cambios seran agregados dependiendo el tipo de reset.  

### git reset --[Tipo] origin/<'rama'>  

Nos permite empatar dos ramas entre si, tomando en cuenta el tipo de reset colocara los cambios.  

`*Nota: Comunmente utilizado para forzar los cambios de una rama a otra, comunmente para solucionar problemas de empate.`  

## git commit -m

Establece un commit dentro del repositorio local.  

`*Nota: sera necesario tener los cambios previamente en el area de pruebas`  

### git commit --am <'mensaje'>

Es un tipo de commit directo, donde se agrega al area de trabajo y a su vez se realiza el commit con el mensaje en cuestion.  

`*Nota: se debe tener cuidado puesto que se realiza directo y puede complicar revertir un cambio en especifico.`  

### git commit --amend

Este tipo de commit destruye el ultimo commit sin perder los cambios y agregando los nuevos generando en un nuevo commit.  

`*Nota: ideal cuando hicimos un commit muy pronto y olvidamos agregar un cambio.Saltara una pantalla donde modificaremos el mensaje del commit presionamos ESC -> (shift+Z)x2`  

### git commit --amend --no-edit

Este tipo de commit agrega nuevos cambios al ultimo commit realizado sin necesidad de modificar el mensaje  

## git LOG -<'num de commits'>

Muestra la lista de commits realizados sobre la ramma en la que se encuentre.  

`*Nota: recomendado consultar solo una lista determinada de commits, evitar problema de bug al consultar sin delimitar.`  

### git shortlog -<'num de commits'>

Muestra la lista de commits realizados de manera mas resumida en un bloque de texto sin separar uno a uno.  

## git diff <'rama'> <'rama'> <'archivo'>

Denota las diferencias entre dos ramas.

### git diff master <'rama'> <'archivo'>

Puede especificarse un archivo de no querer ver todos los cambios en el resto de archivos.  

### git diff --name-status <'rama'> <'rama2'>

Muestra los cambios relalizados a nivel de archivo.  

`*Nota: el resumen de cambios puede parecer abrumador en definitiva una herramienta de visualizacion es la opcion para esto`  

## git push

Sube los commits realizados de manera local al repositorio remoto segun la rama en la que nos situemos.  

`*Nota: si la rama en la que te encuentras fue creada localmente al realizar push puede arrojar error pero obtienes el comando para solucionarlo.`  

## git fetch

Este comando baja todos los cambios(commits) del repositorio remoto al repositorio local.  

`*Nota: Todos los commits obtenidos no son mezclados con las ramas por lo que es necesario lanzar un PULL en cada rama para empatarla completamente.`

## git pull

Baja los cambios del orgien a la rama local en la que se encuentre y genera un merge entre las ramas para empatarlas.  

`*Nota: Es la operacion inversa al PUSH por lo que de igual manera podra tener complicaciones al empatar los cambios y sera necesario resolverlos antes del MERGE.`  

## git merge <'rama'>

Realiza una mezcla de dos ramas, de la que haces referencia a la rama que estas.  

`*Nota: Pueden surgir conflictos que deben ser solucionados igual que en PUSH o PULL.`  

### git merge --abort

En caso de que el merge se vuela complejo al existir un conflicto e intentar resolverlo podemos abortar el merge, regresando los cambios que el merge trajo consigo.  

`*Nota: Solo funciona cuando surja un conflicto, de tener un merge exitoso sin problemas este comando no surtira ningun efecto.`
