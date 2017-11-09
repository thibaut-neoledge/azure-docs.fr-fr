---
title: "Ajouter des nœuds d’extension à un cluster HPC Pack | Microsoft Docs"
description: "Apprenez à augmenter un cluster HPC Pack à la demande dans Azure en ajoutant des instances de rôle de travail s’exécutant dans un service cloud"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 9336743b92130e37b1df2992aab806696f8276aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Ajouter des nœuds d’extension sur demande à un cluster HPC Pack dans Azure
Si vous avez configuré un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) dans Azure, vous aurez peut-être besoin d’une solution permettant de mettre rapidement à l’échelle sa capacité en l’augmentant ou en la réduisant, sans avoir à conserver un ensemble de machines virtuelles de nœud de calcul préconfiguré. Cet article vous montre comment ajouter des nœuds « d’extension » à la demande (instances de rôle de travail en cours d’exécution dans un service cloud) en tant que ressources de calcul à un nœud principal dans Azure. 

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

![Nœuds d’extension][burst]

Les étapes décrites dans cet article vous aident à ajouter rapidement des nœuds Azure à une machine virtuelle à nœud principal HPC Pack basée sur le cloud en vue d’un test ou d’un déploiement pour validation technique. La procédure générale est la même que la procédure « d’extension à Azure » pour ajouter de la capacité de calcul de cloud à un cluster HPC Pack local. Pour obtenir un didacticiel, consultez [Configuration d’un cluster de calcul hybride avec Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Pour obtenir des instructions et considérations détaillées sur les déploiements de production, consultez [Extension à Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Conditions préalables
* **Nœud principal HPC Pack déployé dans une machine virtuelle Azure** : vous pouvez utiliser une machine virtuelle à nœud principal autonome ou une machine virtuelle faisant partie d’un cluster plus grand. Pour créer un nœud principal autonome, consultez [Déployer un nœud principal HPC Pack dans une machine virtuelle Azure](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour connaître les options de déploiement de cluster HPC Pack automatisé, consultez la page [Options pour la création et la gestion d’un cluster HPC Windows dans Azure avec Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Si vous utilisez le [script de déploiement HPC Pack IaaS](hpcpack-cluster-powershell-script.md) pour créer le cluster dans Azure, vous pouvez inclure des nœuds d’extension Azure dans votre déploiement automatisé. Consultez les exemples dans cet article.
  > 
  > 
* **Abonnement Azure** : pour ajouter des nœuds Azure, vous pouvez choisir l’abonnement utilisé pour déployer la machine virtuelle à nœud principal, ou bien un ou plusieurs autres abonnements.
* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs, en particulier si vous choisissez de déployer plusieurs nœuds Azure avec des tailles multicœur. Pour augmenter un quota, [ouvrez une demande de service clientèle en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Étape 1 : Créer un service cloud et un compte de stockage pour les nœuds Azure
Utilisez le portail Azure Classic ou des outils équivalents pour configurer les ressources suivantes qui sont nécessaires pour déployer vos nœuds Azure :

* Un nouveau service cloud Azure
* Un nouveau compte de stockage Azure

> [!NOTE]
> Ne réutilisez pas un service cloud existant dans votre abonnement. 
> 
> 

**Considérations**

* Configurez un service cloud distinct pour chaque modèle de nœud Azure que vous envisagez de créer. Toutefois, vous pouvez utiliser le même compte de stockage pour plusieurs modèles de nœud.
* Nous vous recommandons de rechercher le service cloud et le compte de stockage pour le déploiement dans la même région Azure.

## <a name="step-2-configure-an-azure-management-certificate"></a>Étape 2 : configurer un certificat de gestion Azure
Pour ajouter des nœuds Azure en tant que ressources de calcul, vous devez disposer d’un certificat de gestion sur le nœud principal et charger un certificat correspondant vers l’abonnement Azure utilisé pour le déploiement.

Pour ce scénario, vous pouvez choisir le **certificat de gestion Azure HPC par défaut** que HPC Pack installe et configure automatiquement sur le nœud principal. Ce certificat est utile à des fins de test et pour les déploiements pour validation technique. Pour utiliser ce certificat, chargez le fichier C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer de la machine virtuelle à nœud principal vers l’abonnement. Pour télécharger le certificat dans le [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Paramètres** > **Certificats de gestion**.

Pour obtenir des options supplémentaires pour configurer le certificat de gestion, consultez [Scénarios de configuration du certificat de gestion Azure pour les déploiements d’extension Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Étape 3 : déployer les nœuds Azure sur le cluster
Les étapes pour ajouter et démarrer les nœuds Azure dans ce scénario sont généralement les mêmes que celles utilisées avec un nœud principal local. Pour plus d’informations, consultez les sections suivantes dans [Steps to Deploy Azure Nodes with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Créer un modèle de nœud Azure
* Ajouter les nœuds Azure au cluster Windows HPC
* Démarrer (approvisionner) les nœuds Azure

Après avoir ajouté et démarré les nœuds, vous pouvez les utiliser pour exécuter des tâches de cluster.

Si vous rencontrez des problèmes pendant le déploiement des nœuds Azure, consultez [Dépannage de problèmes liés aux déploiements de nœuds Azure avec Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations sur l’utilisation d’une taille d’instance de calcul intensif pour les nœuds d’extension, consultez [Tailles de machines virtuelles de calcul haute performance](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour savoir comment augmenter ou réduire automatiquement les ressources de calcul Azure en fonction de la charge de travail sur le cluster, consultez [Agrandir et réduire automatiquement les ressources de calcul Azure dans un cluster HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
