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
ms.date: 10/09/2017
ms.openlocfilehash: b9287c7151c96aaccbcda81c111cfe36ead5ab38
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Résolution des problèmes de configuration d’environnement et de déploiement de services
Les informations suivantes peuvent vous aider à déterminer la cause des erreurs susceptibles de se produire lors de la configuration de l’environnement de gestion des modèles.

## <a name="model-management-environment"></a>Environnement de gestion des modèles
### <a name="owner-permission-required"></a>Autorisation du propriétaire requise
Vous devez disposer de l’autorisation du propriétaire sur l’abonnement Azure pour inscrire la ressource Compute Machine Learning.

Vous avez également besoin de l’autorisation du propriétaire pour configurer un cluster pour le déploiement de vos services web.

### <a name="resource-availability"></a>Disponibilité des ressources
Vous devez disposer de suffisamment de ressources disponibles dans votre abonnement pour approvisionner les ressources d’environnement.

### <a name="subscription-caps"></a>Limites d’abonnement
Votre abonnement peut faire l’objet d’une limite de facturation qui peut vous empêcher d’approvisionner les ressources d’environnement. Supprimez cette limite pour activer l’approvisionnement.

### <a name="enable-debug-and-verbose-options"></a>Activation des options de débogage et mode détaillé
Utilisez les indicateurs `--debug` et `--verbose` dans la commande de configuration pour afficher les informations de débogage et de traçage lors de l’approvisionnement de l’environnement.

```
az ml env setup -l <loation> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Déploiement de services
Les informations suivantes peuvent aider à déterminer la cause des erreurs qui se produisent lors du déploiement ou de l’appel du service web.

### <a name="service-logs"></a>Journaux de service
L’option `logs` de l’interface CLI du service fournit des données des journaux de Docker et de Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Pour connaître les autres paramètres des journaux, utilisez l’option `--help` (ou `-h`).

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Option de débogage et mode détaillé
Utilisez l’indicateur `--debug` pour afficher les journaux de débogage pendant le déploiement du service.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Utilisez l’indicateur `--verbose` pour afficher des détails supplémentaires pendant le déploiement du service.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Activation de la journalisation des demandes dans Application Insights
Donnez la valeur true à l’indicateur `-l` lors de la création d’un service web pour activer la journalisation au niveau des demandes. Les journaux des demandes sont écrits dans l’instance Application Insights de votre environnement dans Azure. Recherchez cette instance à partir du nom d’environnement que vous avez utilisé avec la commande `az ml env setup`.

- Donnez la valeur true à `-l` lors de la création du service.
- Ouvrez Application Insights sur le Portail Azure. Utilisez le nom de votre environnement pour trouver l’instance Application Insights.
- Une fois dans Application Insights, cliquez sur Rechercher dans le menu du haut pour afficher les résultats.
- Vous pouvez également accéder à `Analytics` > `Exceptions` > `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Ajout de la gestion des erreurs dans le script de notation
Utilisez la gestion des exceptions dans la fonction `scoring.py`run **de votre script**  pour renvoyer le message d’erreur dans la sortie de votre service web.

Exemple Python :
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Autres problèmes courants
- Si la commande `env setup` échoue, vérifiez que votre abonnement comporte suffisamment de cœurs.
- N’utilisez pas de trait de soulignement (_) dans le nom du service web (comme dans *mon_serviceweb*).
- Réessayez si vous obtenez une erreur **502 Bad Gateway** en appelant le service web. Cela signifie normalement que le conteneur n’a pas encore été déployé sur le cluster.
- Si vous obtenez une erreur **CrashLoopBackOff** lorsque vous créez un service, consultez vos journaux. C’est généralement dû à des dépendances manquantes dans la fonction **init**.
