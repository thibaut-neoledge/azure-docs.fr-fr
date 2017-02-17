---
title: "Montée en puissance d’une application dans Azure | Microsoft Docs"
description: "Découvrez comment activer la montée en puissance d’une application dans Azure App Service pour ajouter des capacités et des fonctionnalités."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: b7ef8386e974486c2d327d0b042eeabdb9701b7e


---
# <a name="scale-up-an-app-in-azure"></a>Montée en puissance d’une application dans Azure
Cet article décrit la mise à l’échelle d’une application web dans Azure App Service. Il existe deux workflows de mise à l’échelle : montée en puissance et augmentation de la taille des instances. Cet article décrit le workflow de montée en puissance.

* [Montée en puissance](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): bénéficiez d’un surcroît de capacité d’UC, de mémoire et d’espace disque, ainsi que de fonctionnalités supplémentaires, comme des machines virtuelles dédiées, des domaines et des certificats personnalisés, des emplacements intermédiaires, la mise à l’échelle automatique, et bien davantage. Pour monter en puissance en modifiant le niveau tarifaire du plan App Service auquel appartient votre application.
* [Augmentation de la taille des instances](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): augmentez le nombre d’instances de machine virtuelle qui exécutent votre application.
  Ce nombre peut atteindre 20 instances, en fonction de votre niveau tarifaire. [d’environnements App Service](../app-service/app-service-app-service-environments-readme.md) au niveau **Premium** tier will further au niveaucrease your scale-out count to 50 au niveaustances. Pour plus d’informations sur l’augmentation de la taille des instances, voir [Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md). Vous y trouverez comment utiliser la mise à l’échelle automatique, qui permet de mettre à l’échelle le nombre d’instances automatiquement en fonction des planifications et des règles prédéfinies.

Ces paramètres de mise à l’échelle sont applicables en quelques secondes et affectent toutes les applications de votre [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Ils ne nécessitent pas de modifier votre code ou de redéployer votre application.

Pour plus d’informations sur la tarification et les fonctionnalités de chaque plan App Service, voir [Détails de la tarification - App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Avant de changer le niveau **Gratuit** d’un plan App Service, commencez par supprimer les [limites de dépense](https://azure.microsoft.com/pricing/spending-limits/) en place pour votre abonnement Azure. Pour voir ou modifier les options de votre abonnement Microsoft Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Montée en puissance de votre niveau de tarification
1. Dans votre navigateur, ouvrez le [portail Azure][portal].
2. Dans le panneau de votre application, cliquez sur **Tous les paramètres**, puis sur **Monter en puissance**.
   
    ![Accédez à la montée en puissance pour votre application Azure.][ChooseWHP]
3. Choisissez votre niveau, puis cliquez sur **Sélectionner**.
   
    Dans l’onglet **Notifications**, la mention **RÉUSSITTE** clignote en vert une fois l’opération terminée.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Mettre à l’échelle des ressources associées
Si votre application dépend d’autres services, tels que Azure SQL Database ou Azure Storage, vous pouvez également faire monter en puissance ces ressources selon vos besoins. Ces ressources ne sont pas mises à l’échelle avec le plan App Service et doivent être mises à l’échelle séparément.

1. Dans **Essentials**, cliquez sur le lien **Groupe de ressources**.
   
    ![Montée en puissance des ressources connexes de votre application Azure](./media/web-sites-scale/RGEssentialsLink.png)
2. Dans la partie **Résumé** du panneau **Groupe de ressources**, cliquez sur une ressource à mettre à l’échelle. La capture d’écran ci-après illustre une ressource Base de données SQL et une ressource Azure Storage.
   
    ![Accédez au panneau du groupe de ressources pour activer la montée en puissance de votre application Azure](./media/web-sites-scale/ResourceGroup.png)
3. Pour une ressource Base de données SQL, cliquez sur **Paramètres** > **Niveau tarifaire** pour mettre à l’échelle le niveau tarifaire.
   
    ![Montée en puissance de la base de données SQL principale pour votre application Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Vous pouvez également activer la [géoréplication](../sql-database/sql-database-geo-replication-overview.md) pour votre instance de base de données SQL.
   
    Dans le cas d’une ressource Azure Storage, cliquez sur **Paramètres** > **Configuration** pour faire évoluer vos options de stockage.
   
    ![Montée en puissance du compte de stockage Azure utilisé par votre application Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>

## <a name="learn-about-developer-features"></a>En savoir plus sur les fonctionnalités de développement
Selon le niveau de tarification, les fonctionnalités orientées développeur disponibles sont les suivantes :

### <a name="bitness"></a>Nombre de bits
* Les modes **De base**, **Standard** et **Premium** prennent en charge les applications 64 bits et 32 bits.
* Les niveaux de plan **Gratuit** et **Partagé** prennent uniquement en charge les applications 32 bits.

### <a name="debugger-support"></a>Prise en charge du débogueur
* Une prise en charge du débogueur est disponible en modes **Gratuit**, **Partagé** et **De base** pour une connexion par plan App Service.
* Une prise en charge du débogueur est disponible en modes **Standard** et **Premium** pour cinq connexions simultanées par plan App Service.

<a name="OtherFeatures"></a>

## <a name="learn-about-other-features"></a>En savoir plus sur les autres fonctionnalités
* Pour obtenir des informations détaillées sur toutes les autres fonctionnalités des plans App Service, notamment concernant la tarification et les fonctionnalités présentant de l’intérêt pour tous les utilisateurs (y compris les développeurs), consultez la page [Détails de la tarification - App Service](https://azure.microsoft.com/pricing/details/web-sites/).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [d’essai d’App Service](https://azure.microsoft.com/try/app-service/) , qui vous permet de créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est nécessaire, et vous ne prenez aucun engagement.
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>Étapes suivantes
* Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* Pour plus d’informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants.
  
    [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Plans de support Microsoft Azure](https://azure.microsoft.com/support/plans/)
  
    [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/)
  
    [Tarification – Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Tailles des machines virtuelles et de Service cloud pour Microsoft Azure][vmsizes]
  
    [Détails de la tarification – App Service](https://azure.microsoft.com/pricing/details/app-service/)
  
    [Détails de la tarification – App Service - Connexions SSL](https://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)
* Pour plus d’informations sur les meilleures pratiques liées à Azure App Service, notamment la création d’une architecture évolutive et résiliente, voir [Best Practices: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)(Meilleures pratiques : Azure App Service Web Apps).
* Pour visionner des vidéos concernant la mise à l’échelle des applications App Service, consultez les ressources suivantes :
  
  * [Quand mettre à l’échelle Sites Web Azure - avec Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Mise à l’échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
  * [Mise à l’échelle de Sites Web Azure - avec Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png



<!--HONumber=Jan17_HO3-->


