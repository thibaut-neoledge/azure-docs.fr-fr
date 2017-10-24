---
title: "Guide de résolution des problèmes de déploiement d’Azure Machine Learning | Microsoft Docs"
description: "Guide de résolution des problèmes de déploiement et de création de services"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>Résolution des problèmes de gestion du modèle
Les informations suivantes peuvent aider à déterminer la cause des erreurs qui se produisent lors du déploiement ou de l’appel du service web.
 
## <a name="1-service-logs"></a>1. Journaux de service
L’option `logs` de l’interface CLI du service fournit des données des journaux de Docker et de Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Pour connaître les autres paramètres des journaux, utilisez l’option `--help` (ou `-h`).

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2. Option de débogage et mode détaillé
Utilisez l’indicateur `--debug` pour afficher les journaux de débogage pendant le déploiement du service.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Utilisez l’indicateur `--verbose` pour afficher des détails supplémentaires pendant le déploiement du service.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3. App Insights
Donnez la valeur true à l’indicateur `-l` lors de la création d’un service web pour activer la journalisation au niveau des demandes. Les journaux des demandes sont écrits dans l’instance Application Insights de votre environnement dans Azure. Recherchez cette instance à partir du nom d’environnement que vous avez utilisé avec la commande `az ml env setup`.

- Donnez la valeur true à `-l` lors de la création du service.
- Ouvrez Application Insights sur le Portail Azure. Utilisez le nom de votre environnement pour trouver l’instance Application Insights.
- Une fois dans Application Insights, cliquez sur Rechercher dans le menu du haut pour afficher les résultats.
- Vous pouvez également accéder à `Analytics` > `Exceptions` > `exceptions take | 10`.


## <a name="4-error-handling-in-script"></a>4. Gestion des erreurs de script
Utilisez la gestion des exceptions dans la fonction `scoring.py`run **de votre script**  pour renvoyer le message d’erreur dans la sortie de votre service web.

Exemple Python :
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5. Autres problèmes courants
- Si la commande `env setup` échoue, vérifiez que votre abonnement comporte suffisamment de cœurs.
- N’utilisez pas de trait de soulignement (_) dans le nom du service web (comme dans *mon_serviceweb*).
- Réessayez si vous obtenez une erreur **502 Bad Gateway** en appelant le service web. Cela signifie normalement que le conteneur n’a pas encore été déployé sur le cluster.
- Si vous obtenez une erreur **CrashLoopBackOff** lorsque vous créez un service, consultez vos journaux. C’est généralement dû à des dépendances manquantes dans la fonction **init**.