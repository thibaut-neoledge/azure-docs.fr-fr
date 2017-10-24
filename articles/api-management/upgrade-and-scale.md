---
title: "Mettre à niveau une instance du service Gestion des API Azure et la mettre à l’échelle | Microsoft Docs"
description: "Cette rubrique explique comment mettre à niveau et mettre à l’échelle une instance du service Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: cf5cd03bd9147ac81b07dc453a919a3c74de44fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Mettre à niveau une instance du service Gestion des API et la mettre à l’échelle 

Les clients peuvent mettre à une instance du service Gestion des API (APIM) en ajoutant et en supprimant des unités. Une **unité** est constituée de ressources Azure dédiées et inclut une capacité de gestion de charge définie, exprimée sous la forme d’un certain nombre d’appels d’API par mois. Le débit et la latence réels dépendent de nombreux facteurs, tels que le nombre et le taux de connexions simultanées, le type et le nombre de stratégies configurées, les tailles des requêtes et des réponses et la latence du back end. 

La capacité et le prix de chaque unité dépendent du **niveau** auquel se trouve l’unité. Vous pouvez choisir entre trois niveaux : **Développeur**, **Standard** et **Premium**. Si vous avez besoin d’augmenter la capacité d’un service au sein d’un niveau, vous devez ajouter une unité. Si le niveau actuellement sélectionné dans votre instance APIM n’autorise pas l’ajout d’unités supplémentaires, vous devez effectuer la mise à niveau vers une couche de niveau supérieur. 

Le prix de chaque unité et la possibilité d’ajouter/de supprimer des unités ou de disposer de certaines fonctionnalités (par exemple, le déploiement de plusieurs régions) dépendent de la couche que vous avez choisie pour votre instance APIM. L’article relatif aux [informations de tarification](https://azure.microsoft.com/pricing/details/api-management/) explique le prix unitaire et les fonctionnalités que vous obtenez dans chaque couche. 

>[!NOTE]
>L’article relatif aux [informations de tarification](https://azure.microsoft.com/pricing/details/api-management/) indique la valeur de capacité unitaire approximative dans chaque couche. Pour obtenir des chiffres plus précis, vous devez consulter un scénario réaliste pour vos API. Pour cela, consultez la section « Comment planifier la capacité » qui suit.

## <a name="prerequisites"></a>Composants requis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Comment planifier la capacité

Pour savoir si vous disposez d’un nombre suffisant d’unités pour gérer votre trafic, effectuez des tests sur les charges de travail attendues. 

Comme indiqué ci-dessus, le nombre de demandes par seconde qu’APIM peut traiter dépend de plusieurs variables, par exemple : le modèle de connexion, la taille de la demande et de la réponse, les stratégies configurées sur chaque API et le nombre de clients envoyant des demandes.

Utilisez les **mesures** (qui utilisent des fonctionnalités Azure Monitor) pour déterminer la quantité de capacité utilisée à un moment donné.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Utiliser le portail Azure pour examiner les mesures 

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
2. Sélectionnez **Métriques**.
3. Sélectionnez la mesure **Capacité** de la zone **Métriques disponibles**. 

    La mesure de capacité vous donne une idée de la quantité de capacité utilisée dans votre locataire. Vous pouvez la tester en augmentant la charge de manière incrémentielle, afin de déterminer quel est le pic. Vous pouvez définir une alerte de mesure vous permettant de savoir à quel moment un événement inattendu survient. Exemple : votre instance APIM accède à la capacité pour plus de 5 minutes. 

    >[!TIP]
    > Vous pouvez configurer l’alerte pour vous avertir lorsque la capacité du service est faible, ou pour qu’elle fasse appel à une application logique qui sera automatiquement mise à l’échelle via l’ajout d’une unité.

## <a name="upgrade-and-scale"></a>Mise à niveau et mise à l’échelle 

Comme indiqué précédemment, vous pouvez choisir entre trois niveaux : **Développeur**, **Standard** et **Premium**. Le niveau **Développeur** doit être utilisé pour évaluer le service ; il ne doit pas être utilisé pour un environnement de production. Le niveau **Développeur** n’est associé à aucun SLA, et vous ne pouvez pas le mettre à l’échelle (ajouter/supprimer des unités). 

Les niveaux **Standard** et **Premium** sont des niveaux de production, associés à des SLA. Vous pouvez les mettre à l’échelle. Le niveau **Standard** peut être mis à l’échelle pour inclure jusqu’à quatre unités. Vous pouvez ajouter n’importe quel nombre d’unités au niveau **Premium**. 

Le niveau **Premium** vous permet de distribuer une seule instance du service Gestion des API sur n’importe quel nombre de régions Azure. Lorsque vous créez un service Gestion des API, l’instance contient une seule unité et se trouve dans une seule région Azure. La région initiale est désignée comme la région **principale**. D’autres régions peuvent être facilement ajoutées. Lorsque vous ajoutez une région, vous spécifiez le nombre d’unités que vous souhaitez allouer. Par exemple, vous pouvez avoir une unité dans la région **principale** et cinq unités, dans une autre région. Vous pouvez vous adapter au trafic que vous rencontrez dans chaque région. Pour en savoir plus, découvrez [comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure](api-management-howto-deploy-multi-region.md).

Vous pouvez effectuer la mise à niveau et la rétrogradation vers n’importe quel niveau. Notez que la rétrogradation peut supprimer certaines fonctionnalités, par exemple les réseaux virtuels ou les déploiements sur plusieurs régions, lorsque vous rétrogradez vers le niveau Standard à partir du niveau Premium.

>[!NOTE]
>Le processus de mise à niveau ou de mise à l’échelle peut durer entre 15 et 30 minutes. Vous recevez une notification lorsqu’il est terminé.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Utiliser le portail Azure pour effectuer la mise à niveau et la mise à l’échelle

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
2. Sélectionnez **Échelle et tarification**.
3. Sélectionnez le niveau souhaité.
4. Spécifiez le nombre d’**unités** à ajouter. Vous pouvez utiliser le curseur ou saisir le nombre d’unités.<br/>
    Si vous choisissez le niveau **Premium**, vous devez d’abord sélectionner une région.
5. Appuyez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure](api-management-howto-deploy-multi-region.md)

