# COMANDOS GIT 2

## git stash
Guarda el estado actual completo de la rama actual incluyendo el estado de los archivos en las diferentes areas, esta opcion es utilizada para guardar los cambios a **medio terminar**  para poder cambiar de ramma descartando los cambios en el area de trabajo.  
`Nota: los STASH duraran en memoria mientras no se borren. Usualmente solo deben ser temporales.`  

### git stach list 
Lista todos los stach realizados.  
`*Nota: no es muy recomendable realizar muchos STACH para no complicar el entendimiento del repositorio.`  

### git stach apply
Regresa los cambios en los archivos.  
`*Nota: regresa solo los cambios a los archivos no el estado de estos.`  

### git stach apply --index 
Regresa los cambios en los archivos y ademas el estado de estos en las diferentes areas.  

### git stach pop 
Regresa los cambios en los archivos y ademas borra el stash  
`*Nota: no podra recuperar el STASH y regresa solo los cambios a los archivos no el estado de estos.`  

### git stach pop --index 
Regresa los cambios en los archivos, el estado de estos y ademas borra el stash   
`*Nota: no podra recuperar el STASH.`  
#### stash@{2}   
se utiliza para regresar a un stash especifico, tanto APPLY como POP puede ser especifico agregando el numero de STASH deseado.

## git tag
Permite establecer una etiqueta en determinado commit de manera que se puede marcar tal commit como una version del proyecto.  
`Nota: Por default el tag toma el ultimo commit salvo que se defina el commit al que va dirigida.`  

### git tag <'v1.4'> -lw <'commit'> 
Establece una etiqueta ligera en determinado commit, en caso de que el tag vaya determinado al ultimo commit realizado se puede omitir el sha-1 del mismo.

### git tag -a <'v1.4'> -m <'commit'>
Establece una etiqueta pesada en determinado commit, en caso de que el tag vaya determinado al ultimo commit realizado se puede omitir el sha-1 del mismo.   

### git tag 
Lista todos los tags.  

### git tag -a -f <'v1.4'> <'commit'>
Sobreescribe un tag.  

### git push origin --tags
Envia todos los tags al repositorio remoto.  

### git push origin <'v1.4'> 
Envia el tag al repositorio remoto.  

### git tag -d <'v1'>
Elimina determinado tag.  

## git GC <'Commit'>
Ejecuta el recolector de basura que se encarga de desechar ciertos cambios y archivos bajo criterios de **git**.   

## Issue #<'num issue'>
Un issue puede ser abierto desde la plataforma de github y puede ser referenciado en un commit para denotar que se trabaja e el issue en cuestion.  
`*Nota: Esto funciona con github y cobrara efecto hasta que se realice el PUSH al origen.`   

## Closes #<num "issue">
Se establece en el commit que se realizara para guardar los cambios y cerrara el issue correspondiente al que se haga referencia.  
`Nota: Esto funciona con github y cobrara efecto hasta que se realice el PUSH al origen.`   

## Crear un repositorio local y llevarlo a github 
Se debe crear un directorio como nombre del proyecto y dentro de este realizar los siguientes comandos.  
`*Nota: Se debe crear en GitHub el proyecto con el nombre identico al del directorio creado localmente completamente vacio para poder realizar el PUSH sin problemas.`  
- [X] git init

- [X] git add .

- [X] git commit -m "[MENSAJE]"

- [X] git remote add origin https://github.com/NOMBRE_USUARIO/NOMBRE_PROYECTO.git

- [X] git push -u origin master


## GIT REBASE
> Funcion para compactar n commits que existen en una rama en uno solo, permitiendo generar un solo commit permitiendo limpiar la rama y facilitar su legibilidad en la linea del tiempo.

- [X] git rebase -i head~[numero de los ultimos commits a compactar]

- [X] seleccionar "s" a los commits a compactar y "pick" al commit que quedara como contenedor

- [X] analizar los mensajes correspondientes a cada commit.  

- [X] git push -f   
`*Nota: el realizar esta accion se debe forzar el PUSH puesto que se modificara le estructura de los commits y sera necesario forcar a que tome la estructura local como actual estructura.`  
