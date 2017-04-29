---
title: "Se connecter à Azure Analysis Services avec Excel | Microsoft Docs"
description: "Découvrez comment vous connecter à un serveur Azure Analysis Services à l’aide de Excel."
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
ms.date: 04/12/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a83e313413d87179b89e78d2b90a08a9bcc92fa1
ms.lasthandoff: 04/17/2017


---
# <a name="connect-with-excel"></a>Connexion avec Excel

Une fois que vous avez créé un serveur dans Azure avant d’y déployer un modèle tabulaire, vous êtes prêt à vous y connecter et à explorer les données. 


## <a name="connect-in-excel"></a>Se connecter dans Excel

La connexion à un serveur dans Excel est prise en charge grâce à la fonction Obtenir les données dans Excel 2016 ou Power Query dans les versions antérieures. La connexion à l’aide de l’Assistant d’importation de table dans Power Pivot n’est pas prise en charge. 

**Se connecter dans Excel 2016**

1. Dans Excel 2016, sur le ruban **Données**, cliquez sur **Obtenir les données externes** > **À partir d’autres sources** > **À partir d’Analysis Services**.

2. Dans l’Assistant de connexion de données, dans **Nom du serveur**, collez le nom du serveur à partir du Presse-papiers. Ensuite, dans **Informations d’identification d’ouverture de session**, sélectionnez **Utiliser le nom d’utilisateur et le mot de passe suivants**, puis tapez le nom d’utilisateur professionnel, par exemple nancy@adventureworks.com, et le mot de passe.

    ![Se connecter à partir de l’ouverture de session Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Dans **Sélectionner une base de données et une table**, sélectionnez la base de données et un modèle ou une perspective, puis cliquez sur **Terminer**.
   
    ![Se connecter à partir du modèle de sélection Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Voir aussi
[Bibliothèques clientes](analysis-services-data-providers.md)   
[Gérer votre serveur](analysis-services-manage.md)     



