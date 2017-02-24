---
title: "Cloudyn assure des tâches de gestion financière informatique pour ses clients grâce aux API Microsoft Azure Usage et RateCard | Microsoft Docs"
description: "Décrit la façon dont Cloudyn, un partenaire de facturation de Microsoft Azure, a intégré les API Azure Billing à son produit.  Ces informations sont particulièrement utiles pour les clients Azure et Cloudyn qui souhaitent utiliser ou essayer les services Cloudyn pour Azure."
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: f1397397-7e92-4c20-9862-ab6b93afefb7
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 02/03/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: c4bb977fc201a1da037ee174c883a5174633d45b


---
# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>Cloudyn assure des tâches de gestion financière informatique pour ses clients grâce aux API Microsoft Azure Usage et RateCard
Cloudyn, un partenaire de développement de Microsoft et un fournisseur de premier plan de fonctionnalités de gestion de cloud, a été choisi pour une évaluation privée des nouvelles API Microsoft Azure Resource Usage et RateCard.  L’API Usage permet d’accéder aux données d’estimation de la consommation Azure pour un abonnement. L’API RateCard fournit des informations de tarification complètes concernant tous les services Azure pour les clients autres que Contrat Entreprise (EA). Lorsqu’elles sont intégrées conjointement, ces API offrent une base d’informations complète pouvant servir de données d’entrée pour les outils de gestion financière informatique (ITFM), tels que ceux fournis par Cloudyn.

## <a name="introduction"></a>Introduction
La prétendue « multiplication » des données découlant de l’utilisation combinée de l’API Usage et de l’API RateCard (usage [units] price[$unit] = utilisation et coûts détaillés) produit la source d’informations de facturation la plus granulaire, précise et fiable disponible à ce jour concernant Azure.

![Vue d’ensemble de la gestion financière informatique][1]

L’exploitation de ces API fournit des informations clés sur l’utilisation et les coûts des clients, permettant ainsi à Cloudyn d’analyser facilement les comptes clients par programme et d’accomplir différentes tâches ITFM pour ses clients.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Intégration des API RateCard et Usage par Cloudyn
L’API RateCard requiert plusieurs paramètres d’entrée, comme les informations de région, la devise et les paramètres régionaux, dont le plus important est OfferDurableID, qui spécifie le type d’offre Azure utilisée par le client (paiement à l’utilisation, plans d’engagement hérités de 6 et 12 mois, offres MSDN, offres MPN, offres promotionnelles et autres). Le paramètre OfferDurableID figure dans le [portail d’utilisation et de facturation Azure](https://account.windowsazure.com/Subscriptions), sous l’intitulé « ID de l’offre » de l’abonnement concerné.

Lors de l’inscription aux services [Cloudyn pour Azure](https://www.cloudyn.com/microsoft-azure/) , les clients peuvent ajouter leur code OfferDurableID, qui permet à Cloudyn d’extraire les informations de tarification appropriées par le biais de l’API RateCard.  Les informations sur les différents types d’offre sont disponibles dans la page [Détails de l’offre Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) .

![Vue d’ensemble du moteur de gestion financière informatique Cloudyn][2]

Cloudyn utilise les API Usage et RateCard, ainsi que l’API Azure Performance, pour créer des couches supplémentaires de visualisation, d’analyse, d’alerte, de création de rapports, de gestion des coûts et de recommandations exploitables, offrant ainsi aux clients Azure un outil ITFM de cloud d’entreprise fiable.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Cas d’utilisation ITFM Cloudyn autorisés par l’intégration des API Usage et RateCard
Les cas d’utilisation ITFM Cloudyn courants qui sont autorisés par l’intégration des API Usage et RateCard sont les suivants :

* **Analyse des coûts** : permet de ventiler les coûts cloud selon n’importe quelle dimension d’identification native (fournisseur, service, compte, région, etc.). Les API Azure Usage et RateCard facilitent cette opération en assurant la répartition la plus granulaire des données d’utilisation et de coût par compte, qui est ensuite regroupée et filtrée par Cloudyn et présentée à l’utilisateur sous la forme d’un graphique ou d’un tableau.

![Graphique en secteurs de l’analyse des coûts][3]

* **Vision à 360° de l’affectation des coûts** : permet aux responsables financiers et informatiques de découvrir la répartition réelle des coûts, les facteurs déterminants et les tendances de leur déploiement cloud. Grâce à cette fonctionnalité, les responsables sont également en mesure d’associer facilement les dépenses de déploiement aux divisions, départements, régions, etc., ce qui fournit des vues d’ensemble sans précédent des coûts cloud et facilite les récupérations des données de facturation et les rétrofacturations d’entreprise. Les API Azure Usage et RateCard font office de données d’entrée pour le moteur d’affectation des coûts Cloudyn, qui complète les API en définissant des méthodes et une logique métier pour l’affectation de ressources non balisées ou non balisables.

![Graphique à 360° de l’affectation des coûts][4]

* **Redimensionnement rentable** : fournit des recommandations de redimensionnement adapté pour les machines virtuelles sous-utilisées, réduisant ainsi les dépenses des clients sur des machines surdimensionnées ou surapprovisionnées. Pour ce faire, Cloudyn examine les métriques d’unité centrale et de RAM des machines virtuelles (par le biais de l’API Performance), les heures d’exécution (à l’aide de l’API Usage) et les coûts (au moyen de l’API RateCard). Cloudyn fournit ensuite des recommandations de redimensionnement adapté en fonction des ressources d’unité centrale ou de RAM sous-utilisées (Performance), puis estime les économies potentielles en multipliant la différence de prix (RateCard) entre les machines virtuelles par l’utilisation réelle (Usage) de la machine sous-utilisée.

![Redimensionnement rentable][5]

* **Recommandations en matière de portage de cloud** : fournit des conseils d’ordre financier sur le portage de cloud. Cloudyn examine les coûts actuels d’un utilisateur liés aux ressources de cloud qui sont déployées sur les principaux fournisseurs de cloud, et les compare avec le coût d’un déploiement équivalent sur Azure. Cloudyn fournit ensuite des recommandations granulaires d’ordre financier par ressource concernant le portage vers Azure. Après avoir évalué le déploiement équivalent requis sur Azure (en fonction des métriques de performances et des préférences utilisateur), Cloudyn utilise l’API RateCard pour évaluer le coût du déploiement équivalent sur Azure.
* **Rapports de performances** : reposant sur l’API Azure Performance, ces rapports fournissent un ensemble de fonctionnalités, depuis la présentation de l’utilisation de l’unité centrale et de la mémoire RAM jusqu’à la fourniture de recommandations d’optimisation. Voici un exemple de rapport sur l’utilisation des instances, qui présente une répartition des instances par utilisation moyenne de l’unité centrale.

![Rapports de performances][6]

* **Gestionnaire de catégories** : puissante fonctionnalité de Cloudyn permettant de classer des ressources de cloud désorganisées. Elle offre aux utilisateurs la possibilité de créer leurs propres catégories uniques (balises) à des fins de mesure et de création de rapports efficaces conformes aux pratiques commerciales. En outre, les utilisateurs peuvent facilement réguler et catégoriser le balisage incohérent (fautes de frappe et autres anomalies) et détecter automatiquement les ressources non balisées afin de garantir la précision de l’affectation des coûts.

![Gestionnaire de catégories][7]

## <a name="video"></a>Vidéo
Voici une courte vidéo expliquant la façon dont un client Azure peut utiliser Cloudyn et les API Azure Billing pour obtenir une vue d’ensemble de ses données de consommation Azure.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Cloudyn-Provides-Cloud-ITFM-Tools-Via-Microsoft-Azure-APIs/player]
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Essayez gratuitement [Cloudyn pour Azure](https://www.cloudyn.com/microsoft-azure/) pour découvrir comment vous pouvez bénéficier d’une transparence des coûts avec des fonctions de reporting et d’analyse personnalisées pour votre déploiement cloud Microsoft Azure.
* Pour découvrir une présentation des API Azure Resource Usage et RateCard, voir [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md) .
* Pour plus d’informations sur ces deux API, qui font partie intégrante de l’ensemble d’API fourni par Azure Resource Manager, consultez la [Référence des API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) .
* Si vous souhaitez vous plonger directement dans l'exemple de code, consultez nos exemples de code d'API de facturation Microsoft Azure sur [Exemples de code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>En savoir plus
* Pour plus d’informations sur les offres Contrat d’entreprise (EA) Microsoft Azure, voir [Licences Azure pour l’entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
* Pour plus d’informations sur Azure Resource Manager, voir l’article [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) .
* Pour plus d’informations sur la suite d’outils nécessaire pour acquérir une bonne compréhension de la dépense cloud, voir l’article du cabinet Gartner [Guide de marché relatif aux outils de gestion financière informatique (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb) (en anglais).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png



<!--HONumber=Feb17_HO2-->


