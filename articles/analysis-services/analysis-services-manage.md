---
title: "Gérer Azure Analysis Services | Microsoft Docs"
description: "Apprenez à gérer un serveur Analysis Services dans Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 13eb8ab1bf3c218f14b4c23ca1a46e9552d55b25
ms.openlocfilehash: a5db6cccf6c3dc55ee2cda59cb9e2ecd2292fcb5


---
# <a name="manage-analysis-services"></a>Gérer Analysis Services
Une fois que vous avez créé un serveur Analysis Services dans Azure, vous devrez peut-être effectuer certaines tâches d’administration et de gestion immédiatement ou un peu plus tard. Par exemple, exécuter le traitement d’actualisation des données, contrôler qui peut accéder aux modèles sur votre serveur ou surveiller l’intégrité de votre serveur. Certaines tâches de gestion ne peuvent être effectuées que dans le portail Azure, d’autres dans SQL Server Management Studio (SSMS), et certaines tâches encore peuvent être effectuées dans les deux.

## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](http://portal.azure.com/) est l’endroit où vous pouvez créer et supprimer des serveurs, surveiller les ressources du serveur, modifier la taille et gérer qui a accès à vos serveurs.  Si vous rencontrez des problèmes, vous pouvez également envoyer une demande de support.

![Obtenir le nom du serveur dans Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
La connexion à votre serveur dans Azure revient à vous connecter à une instance de serveur dans votre entreprise. À partir de SSMS, vous pouvez effectuer la plupart des tâches identiques comme traiter des données ou créer un script de traitement, gérer des rôles et utiliser PowerShell. [Téléchargez et installez la dernière version de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 La principale différence réside dans l’authentification que vous utilisez pour vous connecter à votre serveur. Pour vous connecter à votre serveur Azure Analysis Services, vous devez sélectionner **Authentification par mot de passe Active Directory**.

 Quand vous utilisez SSMS, avant de vous connecter à votre serveur pour la première fois, vérifiez que votre nom d’utilisateur figure dans le groupe Administrateurs Analysis Services. Pour plus d’informations, consultez [Administrateurs de serveur](#server-administrators) plus loin dans cet article.

### <a name="to-connect-with-ssms"></a>Pour se connecter avec SSMS
1. Avant de vous connecter, vous devez obtenir le nom du serveur. Dans **Portail Azure** > Serveur > **Présentation** > **Nom du serveur**, copiez le nom du serveur.
   
    ![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Dans SSMS > **Explorateur d’objets**, cliquez sur **Se connecter** > **Analysis Services**.
3. Dans la boîte de dialogue **Se connecter au serveur**, copiez le nom du serveur puis, dans **Authentification**, choisissez l’une des options suivantes :
   
    **Authentification intégrée Active Directory** pour utiliser l’authentification unique avec Active Directory sur la fédération Azure Active Directory.
   
    **Authentification par mot de passe Active Directory** pour utiliser un compte professionnel. Par exemple, lors d’une connexion à partir d’un ordinateur non joint à un domaine.
   
    Remarque : si vous l’authentification Active Directory ne s’affiche pas, vous devrez peut-être mettre à jour vers la [version la plus récente de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).
   
    ![Se connecter dans SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Étant donné que la gestion de votre serveur dans Azure à l’aide de SSMS est similaire à la gestion d’un serveur local, nous n’irons pas dans les détails ici. Toute l’aide dont vous avez besoin se trouve dans [Gestion d’instance Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) sur MSDN.

## <a name="server-administrators-and-database-users"></a>Administrateurs de serveur et utilisateurs de base de données
Dans Azure Analysis Services, il existe deux types d’utilisateur : les administrateurs de serveur et les utilisateurs de base de données. Les deux types d’utilisateur doivent exister dans Azure Active Directory et être spécifiés par adresse de messagerie d’organisation ou UPN. Cela diffère des bases de données tabulaire locales qui prennent en charge les administrateurs de serveur et les utilisateurs de base de données par des noms d’utilisateur de domaine Windows. Pour en savoir plus, consultez [Manage users in Azure Analysis Services (Gérer des utilisateurs dans Azure Analysis Services)](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Résolution des problèmes de connexion
Quand vous vous connectez à votre serveur à l’aide de SSMS, si (à l’étape 3) vous essayez de vous connecter à l’aide d’un compte non fédéré ou d’un compte qui ne figure pas dans votre annuaire Azure Active Directory, et que vous ne parvenez pas à vous connecter, vous devrez peut-être effacer le cache de connexion. Fermez SSMS avant d’effectuer ces étapes.

1. Dans l’Explorateur de fichiers, accédez à `C:\Users\<user_name>\AppData\Local\`.
2. Supprimez le dossier **AADCacheOM**.
3. Recherchez dans le dossier **Local** des fichiers .dat commençant par le nom **omlibs-tokens-cache**. Si vous en trouvez, supprimez-les.
4. Ouvrez SSMS et répétez les étapes de la section [Pour se connecter avec SSMS](#to-connect-with-ssms) ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
Si vous n’avez pas déjà déployé un modèle tabulaire sur votre nouveau serveur, c’est le moment de le faire. Pour en savoir plus, voir [Déployer sur Azure Analysis Services](analysis-services-deploy.md).

Si vous avez déployé un modèle sur votre serveur, vous êtes prêt à vous connecter à celui-ci à l’aide d’un client ou d’un navigateur. Pour en savoir plus, voir [Obtenir les données du serveur Azure Analysis Services](analysis-services-connect.md).




<!--HONumber=Feb17_HO2-->


