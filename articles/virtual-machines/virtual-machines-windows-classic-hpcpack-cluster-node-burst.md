<properties
 pageTitle="Ajouter des nœuds d’extension à un cluster HPC Pack | Microsoft Azure"
 description="Découvrez comment ajouter des instances de rôle de travail exécutées dans un service cloud à la demande en tant que ressources de calcul à un nœud principal HPC Pack dans Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="01/08/2016"
 ms.author="danlep"/>

# Ajouter des nœuds « d’extension » à la demande (instances de rôle de travail) en tant que ressources de calcul à un cluster HPC Pack dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Il vous montre comment ajouter des nœuds « d’extension » Azure (instances de rôle de travail en cours d’exécution dans un service cloud) à la demande en tant que ressources de calcul à un nœud principal HPC Pack existant dans Azure. Vous pouvez ainsi augmenter la capacité de calcul du cluster HPC dans Azure à la demande, sans avoir à gérer un ensemble de machines virtuelles à nœud de calcul préconfigurées.

![Nœuds d’extension][burst]

>[AZURE.TIP] Si vous utilisez le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour créer le cluster dans Azure, vous pouvez inclure des nœuds d’extension Azure dans votre déploiement automatisé. Consultez les exemples dans cet article.

Les étapes décrites dans cet article vous aident à ajouter rapidement des nœuds Azure à une machine virtuelle à nœud principal HPC Pack basée sur le cloud en vue d’un test ou d’un déploiement pour validation technique. La procédure est essentiellement la même que la procédure « d’extension à Azure » pour ajouter de la capacité de calcul de cloud à un cluster HPC Pack local. Pour obtenir un didacticiel, consultez [Configuration d’un cluster de calcul hybride avec Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Pour obtenir des instructions et considérations détaillées sur les déploiements de production, consultez [Extension à Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

Si vous souhaitez utiliser la taille d’instance de calcul intensif A8 ou A9, consultez [À propos des instances de calcul intensif A8, A9, A10 et A11](virtual-machines-windows-a8-a9-a10-a11-specs.md).

## Configuration requise

* **Nœud principal HPC Pack déployé dans une machine virtuelle Azure** : consultez [Déployer un nœud principal HPC Pack dans une machine virtuelle Azure](virtual-machines-windows-hpcpack-cluster-headnode.md) pour connaître les étapes de création d’un nœud principal de cluster dans le modèle de déploiement classique (Service Management).

* **Abonnement Azure** : pour ajouter des nœuds Azure, vous pouvez choisir l’abonnement utilisé pour déployer la machine virtuelle à nœud principal, ou bien un ou plusieurs autres abonnements.

* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs, en particulier si vous choisissez de déployer plusieurs nœuds Azure avec des tailles multicœur. Pour augmenter un quota, [ouvrez une demande de service clientèle en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.

## Étape 1 : créer un service cloud et un compte de stockage pour ajouter des nœuds Azure

Utilisez le portail Azure Classic ou des outils équivalents pour configurer les éléments suivants, qui sont nécessaires pour déployer vos nœuds Azure :

* Un nouveau service cloud Azure
* Un nouveau compte de stockage Azure

>[AZURE.NOTE] Ne réutilisez pas un service cloud existant dans votre abonnement. En outre, ne déployez pas un package de service cloud distinct et personnalisé sur ce service cloud. HPC Pack déploie automatiquement un package de service cloud quand vous démarrez (approvisionnez) les nœuds Azure.

**Considérations**

* Configurez un service cloud distinct pour chaque modèle de nœud Azure que vous envisagez de créer. Toutefois, vous pouvez utiliser le même compte de stockage pour plusieurs modèles de nœud.

* Vous devez généralement rechercher le service cloud et le compte de stockage pour le déploiement dans la même région.




## Étape 2 : configurer un certificat de gestion Azure

Pour ajouter des nœuds Azure en tant que ressources de calcul, vous devez disposer d’un certificat de gestion sur le nœud principal et charger un certificat correspondant vers l’abonnement Azure utilisé pour le déploiement.

Pour ce scénario, vous pouvez choisir le **certificat de gestion Azure HPC par défaut** que HPC Pack installe et configure automatiquement sur le nœud principal. Ce certificat est utile à des fins de test et pour les déploiements pour validation technique. Pour utiliser ce certificat, chargez le fichier C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer depuis la machine virtuelle à nœud principal vers l’abonnement.

Pour obtenir des options supplémentaires pour configurer le certificat de gestion, consultez [Scénarios de configuration du certificat de gestion Azure pour les déploiements d’extension Azure](http://technet.microsoft.com/library/gg481759.aspx).

## Étape 3 : déployer les nœuds Azure sur le cluster



Les étapes pour ajouter et démarrer les nœuds Azure dans ce scénario sont généralement les mêmes que celles utilisées avec un nœud principal local. Pour plus d’informations, consultez les sections suivantes dans [Steps to Deploy Azure Nodes with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx) :

* Créer un modèle de nœud Azure

* Ajouter les nœuds Azure au cluster Windows HPC

* Démarrer (approvisionner) les nœuds Azure

Après avoir ajouté et démarré les nœuds, vous pouvez les utiliser pour exécuter des tâches de cluster.

Si vous rencontrez des problèmes pendant le déploiement des nœuds Azure, consultez [Troubleshoot Deployments of Azure Nodes with Microsoft HPC Pack] (http://technet.microsoft.com/library/jj159097(v=ws.10).aspx).

## Étapes suivantes

* Pour savoir comment augmenter ou réduire automatiquement les ressources de calcul Azure en fonction de la charge de travail actuelle des travaux et des tâches sur le cluster, consultez [Agrandir et réduire automatiquement les ressources de calcul Azure dans un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png

<!---HONumber=AcomDC_0323_2016-->