---
title: "Créer des groupes de machines virtuelles identiques Azure avec Azure CLI | Microsoft Docs"
description: Explorez les groupes de machines virtuelles identiques.
keywords: machine virtuelle linux, groupes de machines virtuelles identiques
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5b8950619da3f42bc3c92443a7b9e9f2a97067f9
ms.openlocfilehash: 8d274127a4e1947e42b5544c3c1f62c4e48dcee9


---
# <a name="what-are-virtual-machine-scale-sets"></a>Que sont les groupes de machines virtuelles identiques ?
Les groupes de machines virtuelles identiques (VM Scale Sets) vous permettent de gérer plusieurs machines virtuelles comme un ensemble. Globalement, les groupes de machines virtuelles identiques ont les avantages et inconvénients suivants :

Avantages :

1. Haute disponibilité : Chaque groupe place ses machines virtuelles dans un groupe à haute disponibilité avec 5 domaines d’erreur et 5 domaines de mise à jour pour garantir la disponibilité (pour plus d’informations sur les domaines d’erreur et les domaines de mise à jour, consultez la rubrique sur la [disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
2. Intégration facile dans l’équilibreur de charge Azure et App Gateway.
3. Intégration facile dans Azure Autoscale.
4. Déploiement, gestion et nettoyage de machines virtuelles simplifiés.
5. Prise en charge des versions courantes de Windows et Linux, ainsi que d’images personnalisées.

Inconvénients :

1. Impossible d’attacher des disques de données à des instances de machine virtuelle dans un groupe de machines virtuelles identiques. Au lieu de cela, vous devez utiliser Stockage Blob, Azure Files, Azure Tables ou une autre solution de stockage.

## <a name="quick-create-using-azure-cli"></a>Création rapide de machines virtuelles à l’aide de l’interface de ligne de commande
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez la [page d’accueil principale des groupes de machines virtuelles identiques](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Pour rechercher de la documentation, consultez la [page de documentation principale des groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Pour les modèles Resource Manager utilisant des groupes de machines virtuelles identiques, recherchez « vmss » dans le [dépôt Github de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).




<!--HONumber=Jan17_HO4-->


