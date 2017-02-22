---
title: "API et Kit de développement logiciel (SDK) .NET Core de DocumentDB | Microsoft Docs"
description: "Découvrez l’API et le Kit de développement logiciel (SDK) .NET Code, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) .NET Core de DocumentDB."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 855fd05d9addc2e57568067e4d434836ceeed570
ms.openlocfilehash: 21a5f321bd1a3b45cd87c2c9274139c47562cee7


---
# <a name="documentdb-apis-and-sdks"></a>API DocumentDB et Kits de développement logiciel (SDK)
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-net-core-api-and-sdk"></a>API et Kit de développement logiciel (SDK) .NET Core de DocumentDB
<table>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence sur l’API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Exemples**</td><td>[Exemples de code .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) .NET Core de DocumentDB](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Didacticiel d’application web**</td><td>[Développement d’une application web avec DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Infrastructure actuellement prise en charge**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

Le Kit de développement logiciel (SDK) .NET Core de DocumentDB offre les mêmes fonctionnalités que la dernière version du [SDK .NET de DocumentDB](documentdb-sdk-dotnet.md).

> [!NOTE] 
> Le SDK .NET Core de DocumentDB utilisé n’est pas encore compatible avec les applications de la plateforme Windows universelle (UWP). Pour obtenir une version préliminaire du Kit de développement logiciel (SDK) .NET Core qui prend en charge les applications UWP, envoyez un e-mail à l’adresse [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdbcore100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/1.0.0)

Le SDK .NET Core de DocumentDB vous permet de créer rapidement des applications multiplateformes [ASP.NET Core](https://www.asp.net/core) et [.NET Core](https://www.microsoft.com/net/core#windows) à exécuter sur Windows, Mac et Linux. La dernière version du SDK .NET Core de DocumentDB est entièrement compatible avec [Xamarin](https://www.xamarin.com) et peut être utilisée pour créer des applications qui ciblent iOS, Android et Mono (Linux).  

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

Le Kit de développement Logiciel (SDK) .NET Core de DocumentDB en version préliminaire vous permet de créer rapidement des applications multiplateformes [ASP.NET Core](https://www.asp.net/core) et [.NET Core](https://www.microsoft.com/net/core#windows) à exécuter sur Windows, Mac et Linux.

Le Kit de développement Logiciel (SDK) .NET Core de DocumentDB en version préliminaire offre les mêmes fonctionnalités que la dernière version du [Kit de développement logiciel (SDK) .NET de DocumentDB](documentdb-sdk-dotnet.md) et prend en charge les éléments suivants :
* Tous les [modes de connexion](documentdb-performance-tips.md#networking) : mode passerelle, TCP Direct et HTTPs Direct. 
* Tous les [niveaux de cohérence](documentdb-consistency-levels.md): Forte, Session, Obsolescence limitée et Éventuelle.
* [Collections partitionnées](documentdb-partition-data.md). 
* [Comptes de base de données multirégions et géoréplication](documentdb-distribute-data-globally.md).

Si vous avez des questions liées à ce SDK, publiez sur [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) ou envoyez une demande sur le [référentiel GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.0.0](#1.0.0) |21 décembre 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 novembre 2016 |31 décembre 2016 |

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 




<!--HONumber=Jan17_HO2-->


