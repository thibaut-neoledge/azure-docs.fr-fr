---
title: "Bibliothèques clientes requises pour la connexion à Azure Analysis Services | Microsoft Docs"
description: "Décrit les bibliothèques clientes requises pour que les applications clientes et les outils se connectent à Azure Analysis Services"
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
ms.date: 04/14/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 71f85c483f6b87ac34c003ac3695a85396ca945b
ms.lasthandoff: 04/17/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliothèques clientes pour la connexion à Azure Analysis Services

Les bibliothèques clientes sont nécessaires pour que les applications clientes et les outils se connectent aux serveurs Analysis Services. 

Analysis Services utilise trois bibliothèques clientes. ADOMD.NET et Analysis Services Management Objects (AMO) sont des bibliothèques clientes managées. Le fournisseur OLE DB Analysis Services (MSOLAP DLL) est une bibliothèque cliente native. En général, les trois bibliothèques sont installées en même temps. Azure Analysis Services nécessite les dernières versions. 

Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois bibliothèques clientes. Toutefois, selon la version d’Excel, ou selon que des versions plus récentes d’Excel et de Power BI Desktop soient ou non mises à jour tous les mois, il se peut que les bibliothèques clientes installées ne puissent pas être mises à jour vers les dernières versions requises par Azure Analysis Service. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent l’installation manuelle des bibliothèques. Les bibliothèques clientes pour l’installation manuelle sont incluses dans les packs de fonctionnalités SQL Server sous forme de packages distribuables. Toutefois, comme elles sont liées à la version de SQL Server, il se peut qu’elles ne soient pas à jour.  

Les bibliothèques clientes pour les connexions clientes sont différentes des fournisseurs de données requis pour connecter un serveur Azure Analysis Services à une source de données. Pour plus d’informations sur les connexions aux sources de données, consultez [Connexions de source de données](analysis-services-datasource.md).

## <a name="download-the-latest-preview-client-libraries"></a>Télécharger les dernières versions des bibliothèques clientes **préliminaires**  
Utilisez les bibliothèques clientes suivantes pour obtenir les derniers correctifs de bogues et mises à jour. Ces bibliothèques clientes sont recommandées lors de la connexion à la version préliminaire d’Azure Analysis Services ou de SQL Server vNext Analysis Services.

[MSOLAP (amd64) en version préliminaire](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[Version préliminaire de MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO en version préliminaire](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[Version préliminaire de ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>Télécharger les dernières bibliothèques clientes **RTM**  
Utilisez les bibliothèques clientes suivantes si vous êtes dans un environnement de production et avez besoin de versions entièrement publiées et prises en charge.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Étapes suivantes
[Connectez-vous à un serveur Azure Analysis Services](analysis-services-connect.md).

