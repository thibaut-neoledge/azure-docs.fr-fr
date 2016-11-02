<properties 
   pageTitle="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.9" 
   description="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.9" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-.net-2.9-release-notes"></a>Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.9

##<a name="overview"></a>Vue d’ensemble

Ce document contient les notes de publication du Kit de développement logiciel (SDK) Azure pour .NET version 2.9. 

Pour plus d’informations sur les mises à jour de cette version, voir le [billet d’annonce du Kit de développement logiciel (SDK) Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-2.9-for-visual-studio-2015-update-2-and-visual-studio-"15"-preview"></a>Aperçu du Kit de développement logiciel (SDK) 2.9 pour Visual Studio 2015 Update 2 et Visual Studio "15"
 
Cette mise à jour inclut les correctifs suivants :

- Problème lié à REST API Client Generation dans lequel la chaîne "Unknown Type" s'affiche en tant que le nom du dossier de la génération du code et/ou le nom de l'espace de noms est déposé dans le code généré.
- Problème lié à Scheduled WebJobs dans lequel les informations d'authentification n'ont pas pu être transmises au processus d'approvisionnement de Scheduler.

Cette mise à jour inclut les nouvelles fonctionnalités suivantes :

- Prise en charge de services d'application secondaires dans l'onglet "Services" de la boîte de dialogue d'approvisionnement App Service. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Outils Azure Data Lake pour Visual Studio 2015 Update 2
 
Les mises à jour incluent les éléments suivants :

- **Outils Azure Data Lake** pour Visual Studio sont maintenant fusionnés dans la version Azure SDK pour .NET. L'outil est automatiquement installé lorsque vous installez le Kit de développement logiciel (SDK) Azure. 

    L'outil étant fréquemment mis à jour, cliquez [ici](http://aka.ms/datalaketool) pour obtenir les mises à jour.

- **Explorateur de serveurs** vous permet désormais de tout afficher et de créer des entités de métadonnées U-SQL. Pour plus d’informations, consultez [ce](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.


##<a name="hdinsight-tools"></a>Outils HDInsight 

**Outils HDInsight** pour Visual Studio prennent maintenant en charge HDInsight version 3.3, y compris l'affichage des graphiques Tez et d'autres correctifs de langage.


##<a name="azure-resource-manager"></a>Azure Resource Manager 

Cette version ajoute la prise en charge de [KeyVault](../resource-manager-keyvault-parameter.md) pour les modèles ARM.

##<a name="see-also"></a>Voir aussi

[Billet d’annonce du Kit de développement logiciel (SDK) Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)



<!--HONumber=Oct16_HO2-->


