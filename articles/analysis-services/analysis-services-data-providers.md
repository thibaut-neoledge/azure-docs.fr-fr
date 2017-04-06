---
title: "Fournisseurs de données requis pour les connexions clientes à Azure Analysis Services | Microsoft Docs"
description: "Décrit les fournisseurs de données requis pour les clients qui se connectent à Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 14218a7c2acf6b68025095c1ecb60cb480233044
ms.lasthandoff: 03/23/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Fournisseurs de données pour la connexion à Azure Analysis Services

Les fournisseurs de données, ou bibliothèques clientes, sont nécessaires pour que les applications clientes se connectent aux serveurs Analysis Services. 

Analysis Services utilise trois fournisseurs de données. ADOMD.NET et Analysis Services Management Objects (AMO) sont des fournisseurs de données managés. Le fournisseur OLE DB Analysis Services (MSOLAP DLL) est un fournisseur de données en mode natif. En général, les trois fournisseurs sont installés en même temps. Azure Analysis Services nécessite les dernières versions des fournisseurs de données. 

Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois fournisseurs de données. Toutefois, selon la version d’Excel, ou selon que des versions plus récentes d’Excel et de Power BI Desktop soient ou non mises à jour tous les mois, il se peut que les fournisseurs installés ne puissent pas être mis à jour vers les dernières versions requises par Azure Analysis Service. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent l’installation manuelle des fournisseurs. Les fournisseurs de données pour l’installation manuelle sont inclus dans les packs de fonctionnalités SQL Server sous forme de packages distribuables. Toutefois, comme ils sont liés à la version de SQL Server, il se peut qu’ils ne soient pas à jour.  

Les fournisseurs de données pour les connexions clientes sont différents des fournisseurs de données requis pour connecter un serveur Azure Analysis Services à une source de données. Pour plus d’informations sur les connexions aux sources de données, consultez [Connexions de source de données](analysis-services-datasource.md).

## <a name="download-the-latest-preview-data-providers"></a>Télécharger les dernières versions des fournisseurs de données **préliminaires**  
Utilisez les fournisseurs de données suivants pour obtenir les derniers correctifs de bogues et mises à jour. Ces fournisseurs de données sont recommandés lors de la connexion à la version préliminaire d’Azure Analysis Services ou de SQL Server vNext Analysis Services.

[MSOLAP (amd64) en version préliminaire](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_OLEDB.msi)</br>
[Version préliminaire de MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x86/SQL_AS_OLEDB.msi)</br>
[AMO en version préliminaire](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_AMO.msi)</br>
[Version préliminaire de ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>Télécharger les dernières versions des fournisseurs de données **RTM**  
Utilisez les fournisseurs de données suivants si vous êtes dans un environnement de production et avez besoin de versions entièrement publiées et prises en charge.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Étapes suivantes
Avec les données les plus récentes installées, votre application cliente est prête à se connecter à un serveur. Pour en savoir plus sur la connexion à partir d’un client, consultez [Obtenir les données d’Azure Analysis Services](analysis-services-connect.md).

