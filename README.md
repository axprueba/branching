# Estrategia de ramas propuesta (variante de trunk)

## Ramas
* feature/INTER-54    => no despliega automaticamente
* feature/INTER-12    => no despliega automaticamente
* hotfix/XXX          => no despliega automaticamente
* develop             => no despliega automaticamente
* main                => despliega a qa automaticamente v1.0.3-rc (el semver se coge de version.txt)

## Workflows

### Desarrollo
* workflow manual DEPLOY_TEST donde puedes elegir la rama que quieras => genera imagen dev-[nombre_rama]-[commit_hash] y la despliega a test
* si todo va bien en develop, PR a main
* workflow automatico DEPLOY_QA en commits a main                     => genera imagen [semver]-rc y la despliega a qa
* si todo va bien en qa, ejecutas 
* workflow manual DEPLOY_PROD                                         => añade tag al último commit de main, retagea imagen docker v1.0.3-rc a v1.0.3 y la despliega a pro

### Hotfixes
1. Creas la rama hotfix/XXX 
1. Haces los cambios y subes en version.txt (se valida) 
1. Merge a main (genera imagen -rc y despliega qa) 
1. Ejecutas el workflow manual de deploy a pro

### Consideraciones
* Los tags v* son inmutables. Tanto si quieres mergear desde develop o hotfix a main, necesitas subir version.txt
* Cada PR a main se comprueba que el contenido de version.txt es superior al que ya hay en la rama
* En un enfoque trunk-based, la rama main suele recibir la integración continua y se valida de forma frecuente. Es crucial que las pruebas automatizadas cubran la mayor parte de los casos antes de que nada entre en main. De esta forma, al momento de generar una versión release candidate (rc), tendremos un alto grado de confianza en el código.

### Drawbacks
1. En main podría haber cambios no comprobados al 100% y es el único camino de llegar a pro. En la práctica, si no se confía del todo en la estabilidad de main, se podría introducir una rama intermedia para releases, por ejemplo, release/x.y.z, pero eso se acerca a la estrategia GitFlow.





# Estrategia de ramas propuesta (variante de gitflow)

tener una rama release/x.y.z donde se congela el contenido que irá a producción y se hacen pruebas más exhaustivas, puede ser útil. Esto, sin embargo, incrementa la complejidad de ramas y merges, y a veces solo es preferible si tienes muchos cambios que deben esperar mayor validación. Ventaja: previenes que main contenga cambios “inestables” y la gente sabe que solo la rama release es la que va a QA para la próxima entrega.

El flujo sería algo como:

* Haces PR desde develop → release/x.y.z.
* Se desplegaría esa rama de release a qa.
* Se corrigen bugs, se actualiza la versión si es necesario…
* Una vez validada, se mergea release/x.y.z → main y se genera el tag final vX.Y.Z.


## Ramas
feature/INTER-54    => no despliega automaticamente
feature/INTER-12    => no despliega automaticamente
hotfix/XXX          => no despliega automaticamente
develop             => no despliega automaticamente
release/1.0.3       => no despliega automaticamente
main                => despliega a qa automaticamente v1.0.3-rc (el semver se coge de version.txt)

## Workflows

### Desarrollo
* workflow manual DEPLOY_TEST donde puedes elegir la rama que quieras => genera imagen dev-[nombre_rama]-[commit_hash] y la despliega a test
* si todo va bien en develop, creas rama release/1.0.3
* workflow automatico DEPLOY_QA en commits a release/*                => comprueba que el version.txt y el [semver] de release/[semver] coinciden y genera imagen [semver]-rc y la despliega a qa
* si todo va bien en qa, ejecutas 
* workflow manual DEPLOY_PROD                                         => retagea imagen v1.0.3-rc a v1.0.3 y la despliega a pro

### Hotfixes
1. Creas la rama hotfix/XXX 
1. Haces los cambios y subes en version.txt (se valida) 
1. Merge a main (genera imagen -rc y despliega qa) 
1. Ejecutas el workflow manual de deploy a pro

### Consideraciones
* Los tags v* son inmutables. Tanto si quieres mergear desde develop o hotfix a main, necesitas subir version.txt
* Cada PR a main se comprueba que el contenido de version.txt es superior al que ya hay en la rama

### Drawbacks
1. En main podría haber cambios 