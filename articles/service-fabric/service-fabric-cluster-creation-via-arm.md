<properties
   pageTitle="Configurer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager | Microsoft Azure"
   description="Configurez un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager."
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>


# Configurer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager

Cette page vous aide à configurer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager. Pour ce faire, votre abonnement doit avoir suffisamment de cœurs pour déployer les machines virtuelles IaaS qui composeront ce cluster.

## Composants requis

- Pour configurer un cluster sécurisé, vous devez d’abord charger un certificat X.509 sur votre coffre de clés. Vous devez connaître l’URL du coffre source, l’URL du certificat et l’empreinte numérique du certificat.
- Reportez-vous à [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md) pour plus d’informations sur la configuration d’un cluster sécurisé.

## Obtenir un modèle Resource Manager d’exemple

Des modèles Resource Manager d’exemple sont disponibles dans [la galerie de modèles de démarrage rapide Azure sur GitHub](https://github.com/Azure/azure-quickstart-templates). Tous les noms de modèle Service Fabric commencent par « service-fabric ». Vous pouvez explorer le référentiel à partir du terme « fabric » ou faire défiler jusqu’à l’ensemble des modèles d’exemple. Pour vous aider à trouver rapidement ce que vous cherchez, les modèles ont été nommés comme suit :

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) pour indiquer un modèle de cluster non sécurisé de nœud individuel cinq nœuds.

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) pour indiquer un modèle de cluster sécurisé de nœud individuel cinq nœuds compatible WAD.

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) pour indiquer un modèle de cluster sécurisé deux nœuds dix nœuds compatible WAD.

## Créer un modèle Resource Manager personnalisé

Vous pouvez créer un modèle Resource Manager personnalisé de deux façons :

1. Vous pouvez récupérer un modèle d’exemple à partir de la [galerie de modèles de démarrage rapide Azure sur GitHub](https://github.com/Azure/azure-quickstart-templates) et le modifier.

2. Vous pouvez vous connecter au portail Azure et utiliser les pages du portail Service Fabric pour générer le modèle que vous souhaitez personnaliser. Pour ce faire, procédez comme suit :

    a. Connectez-vous au [portail Azure](https://portal.azure.com/).

    b. Passez en revue la procédure de création du cluster, comme décrite dans [Créer un cluster Azure Service Fabric par le biais du portail](service-fabric-cluster-creation-via-portal.md), mais ne cliquez pas sur **Créer**. À la place, accédez à **Résumé** et téléchargez le modèle, comme illustré dans la capture d’écran suivante.

 ![Capture d’écran de la page Cluster Service Fabric montrant le lien qui permet de télécharger un modèle Resource Manager][DownloadTemplate]

## Déployer le modèle Resource Manager sur Azure à l’aide d’Azure PowerShell

Consultez [Déploiement de modèles Resource Manager à l’aide de PowerShell](../resource-group-template-deploy.md) pour obtenir des instructions détaillées sur la façon de déployer le modèle à l’aide de PowerShell.

>[AZURE.NOTE] Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l'état, situation appelée "conservation du quorum". Par conséquent, il est généralement déconseillé d'arrêter tous les ordinateurs du cluster, sauf si vous avez d'abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0511_2016-->