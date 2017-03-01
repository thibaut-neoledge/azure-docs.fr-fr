---
title: Utilisation des proxys dans Azure Functions | Microsoft Docs
description: "Présentation de l’utilisation de Azure Functions Proxies"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Utilisation de Azure Functions Proxies (préversion)

> [!Note] 
> Azure Functions Proxies est actuellement disponible en version préliminaire. La version préliminaire est proposée gratuitement, mais une facturation standard s’applique à l’exécution des proxys. Pour plus d’informations, consultez [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Cet article vous explique comment configurer et utiliser Azure Functions Proxies. Cette fonctionnalité vous permet de spécifier des points de terminaison sur votre application de fonction implémentés par une autre ressource. Vous pouvez utiliser ces proxys pour diviser une API de grande taille en plusieurs applications Function (comme dans une architecture microservice), tout en continuant à présenter une surface API unique aux clients.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>Activation d’Azure Functions Proxies

Les proxies ne sont pas activés par défaut. Vous pouvez créer des proxys lorsque la fonctionnalité est désactivée, mais ils ne s’exécuteront pas. Les étapes suivantes vous montrent comment activer les proxys :

1. Ouvrez le [portail Azure] et accédez à votre application de fonction.
2. Sélectionnez **Paramètres Function App**.
3. Réglez **Activer Azure Functions Proxies (préversion)** sur Activé.

Vous pouvez également revenir ici pour mettre à jour le runtime proxy lorsque de nouvelles fonctionnalités sont disponibles.


## <a name="creating-a-proxy"></a>Création d’un proxy

Cette section vous explique comment créer un proxy dans le portail Functions.

1. Ouvrez le [portail Azure] et accédez à votre application de fonction.
2. Dans le volet de navigation gauche, cliquez sur **Nouveau proxy**.
3. Entrez un nom pour votre proxy.
4. Configurez le point de terminaison exposé sur cette application de fonction en spécifiant le **modèle de routage** et les **méthodes HTTP**. Ces paramètres se comportent selon les règles des [déclencheurs HTTP].
5. Définissez l’**URL principale** sur un autre point de terminaison. Il peut s’agir d’une fonction dans une autre application de fonction ou bien de n’importe quelle autre API.
6. Cliquez sur Créer.

Votre proxy existe désormais sous la forme d’un nouveau point de terminaison de votre application de fonction. Du point de vue du client, cela équivaut à un HttpTrigger dans Azure Functions. Vous pouvez essayer votre nouveau proxy en copiant l’URL de proxy et en le testant avec le client HTTP de votre choix.


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>Modification de demandes du serveur principal

Le paramètre d’URL de serveur principal n’a pas besoin d’être statique. Vous pouvez le conditionner à la saisie des paramètres de l’application ou de la demande.


### <a name="using-request-parameters"></a>Utilisation des paramètres de la demande

Les paramètres utilisés dans le modèle de routage peuvent être utilisés comme entrées de la propriété URL du serveur principal. Les valeurs sont référencées par nom et affichées entre accolades « {} ».

Par exemple, si un proxy dispose d’un modèle de routage du type `/pets/{petId}`, l’URL du serveur principal peut inclure la valeur de `{petId}`, comme dans `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Si le modèle de routage prend fin dans un caractère générique, tel que `/api/{*restOfPath}`, la valeur `{restOfPath}` sera une chaîne représentant les autres segments de chemin d’accès de la demande entrante.


### <a name="using-application-settings"></a>Utilisation des paramètres de l’application

Vous pouvez également référencer les [paramètres de l’application](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) en mettant le nom du paramètre entre signes de pourcentage « % ».

Par exemple, dans une URL de serveur principal de `https://%ORDER_PROCESSING_HOST%/api/orders`, %ORDER_PROCESSING_HOST% sera remplacé par la valeur du paramètre ORDER_PROCESSING_HOST.

> [!TIP] 
> Utilisez des paramètres d’application pour les hôtes de serveur principal lorsque vous avez plusieurs déploiements ou environnements de test. De cette façon, vous avez l’assurance de toujours parler au serveur principal adapté à cet environnement.



## <a name="deployment-methods"></a>Méthodes de déploiement

Les serveurs proxy que vous configurez sont stockés dans un fichier proxies.json, situé à la racine d’un répertoire d’application de fonction. Vous pouvez modifier manuellement ce fichier et le déployer dans le cadre de votre application lors de l’utilisation de l’une des [méthodes de déploiement](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) prises en charge par Functions.

La fonctionnalité doit être activée pour permettre le traitement du fichier. Pour cela, suivez les instructions présentées dans la section [Activation d’Azure Functions Proxies](#enable).

Proxies.json est défini par un objet proxy, composé de proxys nommés et de leurs définitions. Voici un exemple de fichier :

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Chaque proxy a un nom convivial, tel que « proxy1 » dans l’exemple ci-dessus. L’objet de définition de proxy correspondant est défini par les propriétés suivantes :

* **matchCondition** : Obligatoire - un objet définissant les demandes qui déclencheront l’exécution de ce proxy. Il contient deux propriétés partagées avec les [déclencheurs HTTP] :
    * _methods_ : il s’agit d’un tableau des méthodes HTTP auxquelles le proxy répond. À défaut de spécification, le proxy répond à toutes les méthodes HTTP sur le routage.
    * _route_ : Obligatoire - définit le modèle de routage, en contrôlant les URL de demande auxquelles votre proxy répond. Contrairement aux déclencheurs HTTP, il n’y a pas de valeur par défaut.
* **backendUri** : l’URL de la ressource principale à laquelle la demande doit être transmise par proxy. Cette valeur peut être basée sur un modèle, comme décrit dans la section [Modification de demandes du serveur principal](#modify-requests). Si cette propriété n’est pas incluse, Azure Functions répond avec un message HTTP 200 OK.

> [!Note] 
> La propriété de routage Azure Functions Proxies n’honore pas la propriété routePrefix de la configuration d’hôte de Functions. Si vous souhaitez inclure un préfixe tel que /api, il doit être inclus dans la propriété de routage.



[portail Azure]: https://portal.azure.com
[déclencheurs HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
