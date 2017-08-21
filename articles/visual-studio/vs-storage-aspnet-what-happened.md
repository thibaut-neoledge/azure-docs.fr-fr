---
title: "Qu’est-il arrivé à mon projet ASP.NET ? | Microsoft Docs"
description: "Décrit ce qui se produit quand Azure Storage est ajouté à un projet ASP.NET à l’aide des services connectés de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 086c2259aa4686e82f5fd121c4545f573c378414


---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il arrivé à mon projet ASP.NET (service connecté Azure Storage de Visual Studio) ?
## <a name="references-added"></a>Références ajoutées
Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier web.config de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d'informations, consultez la page [ASP.NET](http://www.asp.net).




<!--HONumber=Nov16_HO3-->


