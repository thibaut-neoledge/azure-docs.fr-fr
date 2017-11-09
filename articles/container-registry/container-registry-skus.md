---
title: "Références (SKU) Azure Container Registry"
description: "Comparaisons entre les différents niveaux de service disponibles dans Azure Container Registry"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: 630bc088fcb6d3c7e5bb3a9713107c3fb6653ec6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="azure-container-registry-skus"></a>Références (SKU) Azure Container Registry

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, appelés références (SKU). Ces références offrent une tarification prévisible et plusieurs options pour l’utilisation de votre registre Docker privé dans Azure. Une référence de niveau supérieur fournit de meilleures performances et une meilleure mise à l’échelle. Toutefois, toutes les références offrent les mêmes fonctionnalités de programmation et permettent à un développeur de prendre rapidement en main la version De base, puis d’effectuer une conversion vers la version Standard ou Premium à mesure que l’utilisation du registre augmente.

## <a name="basic"></a>De base
Point d’entrée peu coûteux pour les développeurs qui découvrent Azure Container Registry. Les registres de base ont les mêmes fonctionnalités de programmation que les versions Standard et Premium (intégration de l’authentification Azure Active Directory, suppression d’image et Webhooks). Toutefois, il existe des contraintes de taille et d’utilisation.

## <a name="standard"></a>Standard
Les registres standard offrent les mêmes fonctionnalités que la version De base, avec des limites de stockage et un débit d’image accrus. Les registres standard doivent satisfaire les besoins de la plupart des scénarios de production.

## <a name="premium"></a>Premium
Les registres Premium fournissent des limites supérieures en matière de contraintes de stockage et d’opérations simultanées, ce qui permet de bénéficier de scénarios à haut volume. En plus de la capacité de débit d’image supérieure, la version Premium ajoute des fonctionnalités telles que la [géoréplication](container-registry-geo-replication.md) pour la gestion d’un registre parmi plusieurs régions tout en conservant un registre proche du réseau pour chaque déploiement.

## <a name="classic"></a>Classique
La référence de registre Classique a permis la publication initiale du service Azure Container Registry dans Azure. Les registres classiques sont secondés par un compte de stockage crée par Azure dans votre abonnement, qui limite la capacité d’ACR à fournir des fonctionnalités de niveau supérieur telles que l’augmentation du débit et la géoréplication. En raison de ses capacités limitées, nous prévoyons de déprécier la référence Classique.

> [!NOTE]
> En raison de la dépréciation planifiée de la référence de registre Classique, nous vous recommandons d’utiliser la version De base, Standard ou Premium pour tous les nouveaux registres. Pour plus d’informations sur la conversion de votre registre Classique existant, consultez [Changement de référence](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Matrice de fonctionnalités des références de registre

Le tableau suivant décrit en détail les fonctionnalités et les limites des niveaux de service De base, Standard et Premium.

| Fonctionnalité | De base | Standard | Premium |
|---|---|---|---|---|
| Stockage | 10 Go | 100 Go| 500 Go |
| ReadOps par minute<sup>1, 2</sup> | 1000 | 300 000 | 10 000 000 |
| WriteOps par minute<sup>1, 3</sup> | 100 | 500 | 2000 |
| Bande passante de téléchargement en Mbits/s<sup>1</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Géoréplication | N/A | N/A | [Prise en charge *(préversion)*](container-registry-geo-replication.md) |

<sup>1</sup> Les valeurs *ReadOps*, *WriteOps* et de *bande passante* sont des estimations minimales. ACR s’efforce d’améliorer les performances en fonction de l’utilisation requise.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, plus la récupération du manifeste.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.

## <a name="manage-registry-size"></a>Gérer la taille du registre
Les contraintes de stockage de chaque référence sont destinées à s’aligner avec un scénario classique : De base pour la prise en main, Standard pour la plupart des applications de production, et Premium pour des performances à très grande échelle et la [géoréplication](container-registry-geo-replication.md). Pendant toute la durée de vie de votre registre, vous devez gérer sa taille en supprimant régulièrement le contenu inutilisé.

L’utilisation actuelle du registre est visible dans la **Vue d’ensemble** du registre de conteneurs dans le portail Azure :

![Informations sur l’utilisation du registre dans le portail Azure](media/container-registry-skus/registry-overview-quotas.png)

Vous pouvez gérer la taille de votre registre en supprimant des dépôts dans le portail Azure.

Sous **SERVICES**, sélectionnez **Dépôts**, cliquez sur le dépôt que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

![Supprimer un dépôt dans le portail Azure](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>Changement de référence

Vous pouvez changer la référence d’un registre dans le portail Azure.

Dans la **Vue d’ensemble** du registre dans le portail Azure, sélectionnez **Mettre à jour**, puis sélectionnez une nouvelle **Référence (SKU)** dans la liste déroulante.

![Mettre à jour la référence de registre de conteneurs dans le portail Azure](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>Mise à jour à partir d’une version Classique
Quand vous passez d’un registre Classique à un registre De base, Standard ou Premium, Azure copie les images de conteneurs existantes du compte de stockage associé dans votre abonnement à un compte de stockage géré par Azure. Ce processus peut prendre du temps.

Lors de la conversion, `docker pull` continue à fonctionner, mais `docker push` est bloqué jusqu’à ce que la conversion soit terminée.

Une fois terminé, le compte de stockage de l’abonnement n’est plus utilisé par ACR.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Pourquoi passer de la référence Classique à la référence De base, Standard ou Premium ?

En raison des capacités limitées des registres Classique, nous vous recommandons de mettre à jour vos registres Classique vers le niveau De base, Standard ou Premium. Ces références (SKU) de niveau supérieur intègrent de manière plus étroite le registre avec les fonctionnalités Azure. Il s’agit notamment des fonctionnalités suivantes :

* Intégration d’Azure Active Directory pour l’authentification individuelle (voir [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* Prise en charge de la suppression d’image et de balise
* [Géoréplication](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Notez surtout qu’un registre Classique dépend du compte de stockage qu’Azure a approvisionné automatiquement dans votre abonnement Azure quand vous avez créé le registre. Les références De base, Standard et Premium, quant à elles, tirent parti du *stockage managé*. Autrement dit, Azure gère pour vous et de façon transparente le stockage de vos images. Aucun compte de stockage distinct n’est créé dans votre propre abonnement.

Voici quelques-uns des avantages du stockage managé fourni par les registres De base, Standard et Premium :

* Les images de conteneurs sont [chiffrées au repos](../storage/common/storage-service-encryption.md).
* Les images sont stockées à l’aide du [stockage géoredondant](../storage/common/storage-redundancy.md#geo-redundant-storage), ce qui garantit la sauvegarde de vos images avec la réplication multirégion.
* Possibilité de [basculer d’une référence à une autre](#changing-skus), ce qui permet de bénéficier d’un débit plus élevé quand vous choisissez une référence de niveau supérieur. Avec chaque référence, ACR peut répondre à vos besoins en matière de débit à mesure qu’ils augmentent. L’implémentation sous-jacente de la façon dont ACR accomplit le débit souhaité est exprimée en tant *qu’intention* (en sélectionnant une référence supérieure), sans que vous ayez à gérer les détails de l’implémentation.

## <a name="pricing"></a>Tarification

Pour plus d’informations sur la tarification de chacune des références Azure Container Registry, consultez [Tarification Container Registry](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Étapes suivantes

**Feuille de route Azure Container Registry**

Consultez la [Feuille de route ACR](https://aka.ms/acr/roadmap) sur GitHub pour obtenir des informations sur les fonctionnalités à venir dans le service.

**Azure Container Registry UserVoice**

Proposez et votez pour des suggestions de fonctionnalités dans [ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry).