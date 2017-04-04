---
title: "Création d’un ticket de support pour SQL Data Warehouse | Microsoft Docs"
description: "Création d&quot;un ticket de support dans Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 889776269aa3d722115ea0632b7c3db2775924eb
ms.openlocfilehash: cc3bc8a0463842ba11b9cdcc996319e2d811ce8a
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Création d’un ticket de support pour SQL Data Warehouse
Si vous rencontrez des problèmes avec SQL Data Warehouse, créez un ticket de support pour que notre équipe d’ingénierie puisse vous aider.

> [!NOTE] 
> À compter du 20/12/2016, le contrôle d’intégrité des ressources dans le portail Azure n’est pas exact. Nous travaillons à la résolution de ce problème. 


## <a name="create-a-support-ticket"></a>Création d’un ticket de support
1. Ouvrez le [portail Azure][Azure portal].
2. Dans l'écran d'accueil, cliquez sur la vignette **Aide + Support** .
   
    ![Aide + Support](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. Dans le panneau Aide + Support, cliquez sur **Créer une demande de support**.
   
    ![Nouvelle demande de support](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. Sélectionnez le **Type de demande**.
   
    ![Type de demande](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > Par défaut, le **Quota de DTU** de chaque serveur SQL (par exemple, myserver.database.windows.net) est de 45 000. Ce quota constitue simplement une limite de sécurité. Vous pouvez augmenter votre quota en créant un ticket de support et en sélectionnant *Quota* en tant que type de demande. Pour calculer vos besoins en matière de DTU, multipliez le nombre total de [DWU][DWU] nécessaires par 7,5. Si, par exemple, vous souhaitez héberger deux bases de données DW6000 sur un serveur SQL unique, vous devez demander un quota de DTU de 90 000.  Vous pouvez consulter votre consommation de DTU actuelle dans le panneau SQL Server dans le portail. Les bases de données suspendues et réactivées sont prises en compte dans le quota de DTU. 
   > 
   > 
5. Sélectionnez l’ **abonnement** qui héberge la base de données présentant le problème que vous voulez signaler.
   
    ![abonnement](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Sélectionnez **SQL Data Warehouse** en tant que ressource.
   
    ![Ressource](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. Sélectionnez votre [plan de support Azure][Azure support plan].
   
   * **gestion de la facturation, des abonnements et des quotas** sont pris en charge à tous les niveaux de support.
   * Les problèmes **couverts par la garantie de réparation et d’assistance** sont pris en charge dans les plans de support suivants : [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct] ou [Premier][Premier]. Les problèmes couverts par la garantie de réparation et d'assistance sont des problèmes rencontrés par les clients lors de l'utilisation d'Azure et qui peuvent être raisonnablement imputés à Microsoft.
   * Les **services de tutorat** et d’**expertise pour développeur** sont disponibles aux niveaux de support [Professional Direct][Professional Direct] et [Premier][Premier]. 
     
     Si vous avez un plan de support Premier, vous pouvez également signaler les problèmes liés à SQL Data Warehouse sur le [portail en ligne Microsoft Premier][Microsoft Premier online portal].  Consultez les [plans de support Azure][Azure support plan] pour en savoir plus sur les différents plans disponibles, y compris leur portée, leur temps de réponse, leur tarification, etc.  Pour accéder aux questions fréquemment posées sur le support Azure, consultez la page [FAQ du support Azure][Azure support FAQs].  
     
     ![Plan de support](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. Sélectionnez le **type** et la **catégorie** de problème. Dans cet exemple, nous avons choisi le type de problème « Outils » et la catégorie « Outils clients ». 
   
    ![Catégorie de type de problème](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Décrivez le problème et choisissez le niveau de l'impact commercial.
   
    ![Description du problème](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. Vos **informations de contact** pour ce ticket de support seront pré-remplies. Mettez-les à jour si nécessaire.
    
    ![Informations de contact](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Cliquez sur **Créer** pour envoyer la demande de support.

## <a name="monitor-a-support-ticket"></a>Surveiller un ticket de support
Une fois que vous avez envoyé la demande de support, vous serez contacté par l'équipe de support Azure. Pour vérifier le statut et les détails de votre demande, cliquez sur **Gérer les demandes de support** dans le tableau de bord.

![Vérification du statut](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Autres ressources
En outre, vous pouvez contacter la communauté SQL Data Warehouse sur [Stack Overflow][Stack Overflow] ou le [forum MSDN Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


