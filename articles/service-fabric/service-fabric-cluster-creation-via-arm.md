<properties
   pageTitle="Configuration d’un cluster Service Fabric à l’aide d’un modèle ARM | Microsoft Azure"
   description="Configuration d’un cluster Service Fabric à l’aide d’un modèle ARM."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Configuration d’un cluster Service Fabric à l’aide d’un modèle ARM

Cette page vous permet de configurer un cluster Service Fabric à l’aide d’un modèle ARM. Cela suppose que votre abonnement a suffisamment de cœurs pour déployer les machines virtuelles IaaS qui composeront ce cluster

## Composants requis

- Si vous souhaitez configurer un cluster sécurisé, assurez-vous d'avoir téléchargé un certificat X509 sur votre coffre de clés. Vous aurez besoin de l'URL du coffre source, de l’URL du certificat et de l'empreinte numérique du certificat.
-  Reportez-vous à [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md) pour plus d'informations sur la procédure à suivre.

## Acquisition d'un modèle ARM d’exemple

1. Des modèles ARM d’exemple sont disponibles dans [la galerie de modèles de démarrage rapide Azure sur github](https://github.com/Azure/azure-quickstart-templates). Tous les noms de modèles de Service Fabric commencent par « service-fabric-cluster-... ». Vous pouvez rechercher « fabric » dans le référentiel ou simplement faire défiler l'ensemble des modèles d’exemple.
2. Pour vous aider à trouver rapidement ce que vous cherchez, les modèles ont été nommés comme suit :
	1. [service-fabric-cluster-5-node-1-nodetype](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype) pour indiquer un modèle de cluster non sécurisé de nœud individuel 5 nœuds.
	2. [service-fabric-cluster-5-node-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad) pour indiquer un modèle de cluster non sécurisé de nœud individuel 5 nœuds compatible WAD.
	3. [service-fabric-cluster-5-node-secure-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad) pour indiquer un modèle de cluster sécurisé de nœud individuel 5 nœuds compatible WAD.
	4. [service-fabric-cluster-10-node-secure-2-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad) pour indiquer un modèle de cluster sécurisé deux nœuds 10 nœuds compatible WAD.
	

## Création d’un modèle ARM personnalisé

2. Deux choix s’offrent à vous 
	1. Vous pouvez récupérer un modèle d’exemple à partir de [la galerie de modèles de démarrage rapide Azure sur github](https://github.com/Azure/azure-quickstart-templates) et le modifier.
	2. Connectez-vous au portail Azure et utilisez les pages du portail Service Fabric pour générer le modèle que vous souhaitez personnaliser. La procédure à suivre est décrite ci-dessous.
3. Connectez-vous au portail Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).
2. Passez en revue la procédure de création du cluster, comme décrite dans [Création d’un cluster Service Fabric via le portail](service-fabric-cluster-creation-via-portal.md), mais ne cliquez pas sur ****créer**. À la place, accédez au résumé et téléchargez le modèle. ![DownloadTemplate][DownloadTemplate]

## Déploiement du modèle ARM sur Azure à l'aide d'Azure PS

Reportez-vous à [Déploiement de modèles ARM à l’aide de PS](resource-group-template-deploy.md) pour obtenir des instructions détaillées sur la procédure à suivre.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md) 
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1203_2015-->