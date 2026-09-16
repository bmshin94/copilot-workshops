---
slug: es-es/vscode/7-foundry-toolkit
title: "Opcional: Incorporar Foundry"
description: "Crea un Backer Concierge basado en el catálogo con VS Code y Microsoft Foundry Toolkit en tres módulos específicos."
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
---

| [← Lección anterior: Iterar sobre el trabajo de GitHub Copilot][previous-lesson] |
|:--|

El itinerario obligatorio de VS Code termina con el ejercicio 6. Esta ampliación opcional utiliza GitHub Copilot Chat y Microsoft Foundry Toolkit en VS Code para convertir el catálogo de Tailspin en un Backer Concierge, implementarlo como agente hospedado y conectarlo al sitio a través de un proxy local.

## Escenario

Los patrocinadores hacen preguntas que los filtros no pueden responder: ¿qué juego encaja con alguien a quien le encantan los juegos de palabras sobre git, o qué hace que un juego de puzles sea más adecuado que otro? Tailspin Toys necesita un asistente que recomiende títulos reales del catálogo, haga una pregunta aclaratoria cuando sea necesario y se gane la confianza de los usuarios al reconocer que las cifras de financiación u otros datos no están disponibles.

## Módulos

Cada módulo termina con un resultado funcional. Se mantienen el mismo repositorio del participante, la misma rama de funcionalidad y el mismo proyecto de Foundry durante los tres módulos; el proyecto no se vuelve a crear entre módulos.

| Módulo | Resultado al finalizar |
|--------|-----------------------|
| [1. Preparar un proyecto y un modelo][module-1] | Catálogo exportado y modelo implementado probado frente a las reglas de fundamentación |
| [2. Crear e implementar un agente][module-2] | Agente local depurado y agente hospedado probado |
| [3. Conectar el agente al sitio][module-3] | Proxy local y widget accesible probados de extremo a extremo |

> [!IMPORTANT]
> Microsoft Foundry Toolkit y los agentes hospedados están en versión preliminar pública. Estos módulos crean recursos de Azure facturables, incluidos una implementación de modelo y un agente hospedado. Los permisos de la suscripción, la disponibilidad regional, la cuota y el coste pueden limitar la participación.

## Elegir un punto de partida

La ampliación parte de tu repositorio de Tailspin Toys, no del repositorio de documentación del taller.

1. Confirma que el trabajo obligatorio del taller está guardado, confirmado mediante un commit y enviado al repositorio remoto antes de empezar la funcionalidad opcional.
2. Revisa la suscripción de Azure seleccionada, la región, los permisos, la cuota y los costes estimados antes de aprobar la creación de recursos.
3. Empieza por [Preparar un proyecto y un modelo][module-1]. Si retomas el trabajo, utiliza el punto de control de ese módulo para identificar el repositorio, la rama, el proyecto y la implementación existentes antes de continuar.
4. Si paras después de cualquier módulo, sigue las indicaciones de [Eliminar los recursos][cleanup], salvo que decidas conservarlos para el siguiente módulo y aceptes los costes continuados.

## Eliminar los recursos

La eliminación de recursos también se aplica si paras tras crear únicamente el proyecto y el modelo. No requiere haber generado la estructura de un agente ni disponer de un proyecto de `azd`.

> [!WARNING]
> La eliminación de recursos es destructiva. `azd down --purge` puede eliminar de forma permanente el proyecto de Foundry, la implementación del modelo y el agente hospedado gestionados por esa implementación. Al eliminar un grupo de recursos, se elimina todo su contenido. Nunca elimines un grupo de recursos compartido ni recursos que pertenezcan a otro ejercicio o a otra persona.

1. Guarda el código que quieras conservar en el repositorio de Tailspin Toys, sin incluir credenciales ni archivos de entorno locales. Detén los procesos del agente local, del host de Functions y del sitio que hayas iniciado.
2. En Azure, verifica la suscripción activa e inspecciona los recursos de `rg-tailspin-toys`. Confirma qué proyecto, modelo, agente hospedado y recursos auxiliares pertenecen a este ejercicio opcional antes de eliminar nada.
3. Si has implementado con `azd`, abre el directorio de implementación generado y verifica el entorno de `azd` seleccionado y los recursos que gestiona. Solo si están dedicados a este ejercicio, ejecuta el siguiente comando y revisa la confirmación. Esta es una opción de eliminación, no un requisito previo para el siguiente paso.

   ```bash
   azd down --purge
   ```

4. Si has creado el proyecto y el modelo sin una implementación de `azd`, o si quedan recursos después, utiliza Azure para eliminar únicamente los recursos que te pertenecen. Si, y solo si, todo el grupo `rg-tailspin-toys` está dedicado a este ejercicio y la CLI de Azure apunta a la suscripción verificada, el siguiente comando es una alternativa a la eliminación individual. Omite la confirmación y devuelve el control antes de que termine la eliminación.

   ```bash
   az group delete --name rg-tailspin-toys --yes --no-wait
   ```

5. Confirma en Azure que la eliminación ha terminado y que no quedan recursos facturables de este ejercicio. Si los permisos impiden eliminarlos, pide al propietario de la suscripción que elimine los recursos identificados; detener únicamente los procesos locales no detiene los cargos de Azure.

## Recursos

- [Foundry Toolkit para Visual Studio Code][foundry-toolkit]
- [Información general de la extensión de agentes de Microsoft Foundry][foundry-extension]

| [Siguiente módulo: Preparar un proyecto y un modelo →][module-1] |
|--:|

[previous-lesson]: ../6-iterating/
[module-1]: 1-project-and-model/
[module-2]: 2-build-and-deploy/
[module-3]: 3-connect-to-site/
[cleanup]: #eliminar-los-recursos
[foundry-toolkit]: https://code.visualstudio.com/docs/intelligentapps/overview
[foundry-extension]: https://learn.microsoft.com/azure/developer/azure-developer-cli/extensions/azure-ai-foundry-extension
