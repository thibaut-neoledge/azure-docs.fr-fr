---
title: Que sont les jeux de mise à l’échelle de machine virtuelle ? | Microsoft Docs
description: En savoir plus sur les jeux de mise à l’échelle de machine virtuelle.
keywords: machine virtuelle linux, jeux de mise à l’échelle de machine virtuelle
services: virtual-machines-linux
documentationcenter: ''
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: gatneil

---
# Que sont les jeux de mise à l’échelle de machine virtuelle ?
Les jeux de mise à l’échelle de machine virtuelle vous permettent de gérer plusieurs machines virtuelles sous forme d’un ensemble. À un niveau élevé, les jeux de mise à l’échelle ont les avantages et inconvénients suivants :

Avantages :

1. Haute disponibilité : Chaque jeu de mise à l’échelle place ses machines virtuelles dans un groupe à haute disponibilité avec 5 domaines d’erreur et 5 domaines de mise à jour pour garantir la disponibilité (pour plus d’informations sur les domaines d’erreur et les domaines de mise à jour, consultez la rubrique sur la [disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md)). 
2. Intégration facile dans l’équilibreur de charge Azure et App Gateway.
3. Intégration facile dans Azure Autoscale.
4. Déploiement, gestion et nettoyage de machines virtuelles simplifiés.
5. Prise en charge des versions courantes de Windows et Linux, ainsi que d’images personnalisées.

Inconvénients :

1. Impossible d’attacher des disques de données à des instances de machine virtuelle dans un jeu de mise à l’échelle. Au lieu de cela, vous devez utiliser le stockage d’objets Blob (Blob Storage), des fichiers Azure, des tables Azure ou une autre solution de stockage.

## Création rapide de machines virtuelles à l’aide de l’interface de ligne de commande
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## Étapes suivantes
Pour plus d’informations, consultez la [page d’accueil principale pour les jeux de mise à l’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Pour rechercher de la documentation, consultez la [page de documentation principale pour les jeux de mise à l’échelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Pour les modèles Resource Manager utilisant des jeux de mise à l’échelle, recherchez « vmss » dans le [dépôt Github de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0413_2016-->