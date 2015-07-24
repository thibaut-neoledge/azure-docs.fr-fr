<properties
   pageTitle="Aperçu du support d’Azure Resource Manager pour l’équilibrage de charge | Microsoft Azure"
   description="Utilisation de Powershell pour l’équilibrage de charge avec Azure Resource Manager (ARM) en version préliminaire Utilisation de modèles pour l'équilibrage de charge"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />


# Support d’Azure Resource Manager pour l’équilibrage de charge 

Azure Resource Manager (ARM) est la nouvelle infrastructure de gestion des services dans Azure. L’équilibrage de charge Azure peut maintenant être géré à l’aide des outils et des API basés sur Azure Resource Manager. Pour plus d’informations sur Azure Resource Manager, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-preview-portal-using-resource-groups.md).

## Concepts

Avec ARM, l’équilibrage de charge Azure contient les ressources enfants suivantes :

- Configuration d'une adresse IP frontale : un équilibrage de charge peut inclure une ou plusieurs adresses IP frontales, également appelées « adresses IP virtuelles ». Ces adresses IP servent d'entrée pour le trafic.

- Pool d'adresses principal : il s'agit des adresses IP associées à la carte d’interface réseau (NIC) des machines virtuelles vers lesquelles la charge sera distribuée.

- Règles d'équilibrage de charge : une propriété de règle mappe une combinaison donnée d'adresse IP et de port frontaux vers un ensemble de combinaisons d'adresses IP et de port principaux. Avec une seule définition d'une ressource d'équilibrage de charge, vous pouvez définir plusieurs règles d'équilibrage de charge, chaque règle reflétant une combinaison d'une adresse IP et d'un port frontaux d'une part, et d'une adresse IP et d'un port principaux d'autre part, associés à des machines virtuelles.

- Sondes : les sondes vous permettent d'effectuer le suivi de l'intégrité des instances de machine virtuelle. En cas d'échec d'une sonde d'intégrité, l'instance de machine virtuelle est automatiquement mise hors service.

- Règles NAT de trafic entrant : règles NAT définissant le trafic entrant qui transite via l'adresse IP frontale et est distribué à l'adresse IP principale.


![](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/resource-groups-networking/20150429054039/figure5.png)



## Modèles de démarrage rapide
Azure Resource Manager vous permet d’approvisionner vos applications à l'aide d'un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l'application

Les modèles incluent des machines virtuelles, des réseaux virtuels, des groupes à haute disponibilité, des interfaces réseau (NIC), des comptes de stockage, des équilibrages de charge, des groupes de sécurité réseau et des adresses IP publiques. À l’aide de ces modèles, vous pouvez créer tout ce dont vous avez besoin pour une application complexe avec un simple fichier que vous pouvez archiver et avec lequel vous pouvez collaborer.

[En savoir plus sur les modèles](http://go.microsoft.com/fwlink/?LinkId=544798)

[En savoir plus sur les ressources de réseau](../resource-groups-networking)

Les modèles utilisant l'équilibrage de charge Azure se trouvent dans un [référentiel GitHub](https://github.com/Azure/azure-quickstart-templates) qui héberge un ensemble de modèles générés par la communauté

Exemples de modèles :

- [2 machines virtuelles dans un équilibrage de charge et les règles d'équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544799)

- [2 machines virtuelles dans un réseau virtuel avec un équilibrage de charge interne et les règles d’équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544800)

- [2 machines virtuelles dans un équilibrage de charge et la configuration des règles NAT sur l'équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544801)


## Configuration de l'équilibrage de charge Azure avec PowerShell ou la CLI

Les [cmdlets de mise en réseau Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) peuvent être utilisées pour créer un équilibrage de charge. Prise en main des cmdlets ARM et des API REST

- [Création d’un équilibrage de charge à l’aide d’Azure Resource Manager](../load-balancer-arm-powershell)

- [Utilisation de la CLI Azure avec Azure Resource Manager](../xplat-cli-azure-resource-manager)

- [API REST de l'équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## Voir aussi

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
 

<!----HONumber=July15_HO2-->