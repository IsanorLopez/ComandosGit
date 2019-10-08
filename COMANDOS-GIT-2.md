# <p style="text-align: center;"> COMANDOS GIT 2. </p>

## <p style="text-align: center;"> Git STASH <p>
> **Def:** Guarda el estado actual completo de la rama actual incluyendo el estado de los archivos en las diferentes areas, esta opcion es utilizada para guardar los cambios a **medio terminar**  para poder cambiar de ramma descartando los cambios en el area de trabajo.  
>`Nota: los STASH duraran en memoria mientras no se borren. Usualmente solo deben ser temporales.`  
> > **git stach list** Lista todos los stach realizados.   
> >`Nota: no es muy recomendable realizar muchos STACH para no complicar el entendimiento del repositorio.`  
> > **git stach apply** regresa los cambios en los archivos.   
> >`Nota: regresa solo los cambios a los archivos no el estado de estos.`  
> > **git stach apply --index** Regresa los cambios en los archivos y ademas el estado de estos en las diferentes areas.   
> > **git stach pop** Regresa los cambios en los archivos y ademas borra el stash   
> >`Nota: no podra recuperar el STASH y regresa solo los cambios a los archivos no el estado de estos.`  
> > **git stach pop --index** Regresa los cambios en los archivos, el estado de estos y ademas borra el stash   
> >`Nota: no podra recuperar el STASH.`
> > > **stash@{2}:** tanto APPLY como POP puede ser especifico agregando el numero de STASH deseado.

## <p style="text-align: center;"> Git TAG <"Commit"> <p>
> **Def:** Agrega una etiqueta a un commit en especifico puede ser una version o un texto para definir mejor el commit y puede tener varios tags   

## <p style="text-align: center;"> Git GC <"Commit"> <p>
> **Def:** Ejecuta el recolector de basura que se encarga de desechar ciertos cambios y archivos bajo criterios de **git**.   

## <p style="text-align: center;"> Issue #<num "issue"> <p>
> **Def:** Un issur puede ser abierto desde la plataforma de github y puede ser referenciado en un commit para denotar que se trabaja e el issue en cuestion.  
> `Nota: Esto funciona con github y cobrara efecto hasta que se realice el PUSH al origen.`   

## <p style="text-align: center;"> Closes #<num "issue"> <p>
> **Def:** Se establece en el commit que se realizara para guardar los cambios y cerrara el issue correspondiente al que se haga referencia.  
> `Nota: Esto funciona con github y cobrara efecto hasta que se realice el PUSH al origen.`   

## <p style="text-align: center;"> Crear un repositorio local y llevarlo a github #<num "issue"> <p>
> Se debe crear un directorio como nombre del proyecto y dentro de este realizar los siguientes comandos.  
> `Nota: Se debe crear en GitHub el proyecto con el nombre identico al del directorio creado localmente completamente vacio para poder realizar el PUSH sin problemas.`
~~~
git init

git add .

git commit -m "[MENSAJE]"

git remote add origin https://github.com/NOMBRE_USUARIO/NOMBRE_PROYECTO.git

git push -u origin master
~~~

