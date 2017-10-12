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
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a96e7fe671dc051da35168fa7b49ecba53b4c4a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliothèques clientes pour la connexion à Azure Analysis Services

Les bibliothèques clientes sont nécessaires pour que les applications clientes et les outils se connectent aux serveurs Analysis Services. 

Analysis Services utilise trois bibliothèques clientes. ADOMD.NET et Analysis Services Management Objects (AMO) sont des bibliothèques clientes managées. Le fournisseur OLE DB Analysis Services (MSOLAP DLL) est une bibliothèque cliente native. En général, les trois bibliothèques sont installées en même temps. Azure Analysis Services nécessite les dernières versions. 

Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois bibliothèques clientes. Toutefois, selon la version ou la fréquence des mises à jour, les bibliothèques clientes peuvent ne pas être les dernières versions requises par Azure Analysis Services. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent l’installation manuelle des bibliothèques. Les bibliothèques clientes pour l’installation manuelle sont incluses dans les packs de fonctionnalités SQL Server sous forme de packages distribuables. Toutefois, elles sont liées à la version de SQL Server et il se peut qu’elles ne soient pas à jour.  

Les bibliothèques clientes pour les connexions clientes sont différentes des fournisseurs de données requis pour connecter un serveur Azure Analysis Services à une source de données. Pour plus d’informations sur les connexions aux sources de données, consultez [Connexions de source de données](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Télécharger les dernières bibliothèques clientes  
Utilisez les bibliothèques clientes suivantes si vous êtes dans un environnement de production et avez besoin de versions entièrement publiées et prises en charge.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Étapes suivantes
[Connexion avec Excel](analysis-services-connect-excel.md)    
[Connexion avec Power BI](analysis-services-connect-pbi.md)
