<properties
   pageTitle="Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure"
   description="Offre une présentation conceptuelle des API Azure Billing Usage et RateCard, qui fournissent des vues d’ensemble des tendances et de la consommation des ressources Azure."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="07/7/2015"
   ms.author="mobandyo;bryanla"/>

# Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure 

Les clients et partenaires doivent se révéler en mesure de prédire et gérer leurs coûts Azure de manière efficace. Lorsqu’ils passent d’un modèle Capex (dépenses d’investissement) à un modèle Opex (dépenses d’exploitation), ils doivent également être à même d’effectuer une analyse de récupération des données de facturation et de rétrofacturation, ainsi que de fournir un mode d’estimation et de facturation cohérent, notamment dans le cas des déploiements cloud de grande taille.

Les API Azure Resource Usage et RateCard décrites dans cet article répondent à ces besoins en offrant de nouvelles vues d’ensemble de votre consommation des ressources Azure.

## Présentation des API Azure Resource Usage et RateCard 

Les API Azure Resource Usage et RateCard sont implémentées en tant que fournisseur de ressources et font partie intégrante de la famille d’API exposées par Azure Resource Manager.

### API Azure Resource Usage (version préliminaire)
Les clients et les partenaires peuvent utiliser l’API Azure Resource Usage pour obtenir une estimation de leurs données de consommation Azure. Cette API offre les fonctionnalités suivantes :
	
- **Contrôle d’accès en fonction du rôle Azure** : les clients et partenaires peuvent configurer leurs stratégies d’accès dans le [portail Azure en version préliminaire](https://portal.azure.com) ou par le biais des [applets de commande Azure PowerShell](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications qui peuvent accéder aux données d’utilisation de l’abonnement. Les appelants doivent utiliser les jetons Azure Active Directory standard pour l’authentification. L’appelant doit également être ajouté au rôle Lecteur, Propriétaire ou Collaborateur pour être en mesure d’accéder aux données d’utilisation relatives à un abonnement Azure spécifique.

- **Agrégations horaires ou quotidiennes** : les appelants peuvent indiquer s’ils souhaitent visualiser leurs données d’utilisation Azure par intervalles de temps horaires ou quotidiens. Par défaut, les données sont présentées par jour.

- **Métadonnées d’instance fournies (balises de ressource incluses)** : la réponse fournira les détails de niveau instance, comme l’URI de ressource complet (/subscriptions/{subscription-id}/..), ainsi que les informations de groupe de ressources et les balises de ressource. Ces informations aideront les clients à allouer de façon déterministe et par programme l’utilisation par les balises, pour les cas d’utilisation comme la facturation interne.

- **Fourniture de métadonnées sur les ressources** : la réponse fournira également des détails sur les ressources, comme le nom du compteur, la catégorie du compteur, la sous-catégorie du compteur, l’unité et la région, afin d’offrir aux appelants une meilleure compréhension des ressources consommées. Nous nous efforçons également d’aligner la terminologie des métadonnées de ressource dans l’ensemble du portail Azure, des fichiers CSV sur l’utilisation d’Azure et sur la facturation Contrat Entreprise (EA), ainsi que dans toutes les autres expériences destinées au public, afin de permettre aux clients de mettre en corrélation les données des différentes expériences.

- **Utilisation pour tous les types d’offre** : les données d’utilisation seront accessibles pour tous les types d’offre, tels que Paiement à l’utilisation, MSDN, Engagement monétaire, Crédit monétaire et Contrat Entreprise (EA).

### API Azure Resource RateCard (version préliminaire)
Les clients et les partenaires peuvent utiliser l’API Azure Resource RateCard pour obtenir la liste des ressources Azure disponibles, ainsi qu’une estimation des informations de prix pour chacune de ces ressources. Cette API offre les fonctionnalités suivantes :

- **Contrôle d’accès en fonction du rôle Azure** : les clients et partenaires peuvent configurer leurs stratégies d’accès dans le [portail Azure en version préliminaire](https://portal.azure.com) ou par le biais des [applets de commande Azure PowerShell](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications qui peuvent accéder aux données RateCard. Les appelants doivent utiliser les jetons Azure Active Directory standard pour l’authentification. L’appelant doit également être ajouté au rôle Lecteur, Propriétaire ou Collaborateur pour être en mesure d’accéder aux données d’utilisation relatives à un abonnement Azure spécifique.
	
- **Prise en charge des offres Paiement à l’utilisation, MSDN, Engagement monétaire et Crédit monétaire (offre EA non prise en charge)** : cette API fournit des informations de tarif au niveau des offres Azure, par opposition aux informations de niveau abonnement. L’appelant de cette API doit transmettre les informations d’offre pour obtenir les détails et les tarifs des ressources. Étant donné que les offres EA présentent des tarifs personnalisés par inscription, nous ne sommes pas en mesure de fournir les tarifs EA à ce stade.

## Scénarios

Voici quelques-uns des scénarios autorisés par l’utilisation combinée des API Usage et RateCard :

- **Dépense Azure sur le mois** : les clients peuvent utiliser les API Usage et RateCard simultanément pour obtenir une meilleure vue d’ensemble de leur dépense cloud au cours du mois en analysant les estimations d’utilisation et de coûts des intervalles de temps horaires et quotidiens. 

- **Configuration d’alertes** : les clients et partenaires peuvent configurer des alertes basées sur les ressources ou sur les valeurs monétaires et appliquer ces alertes à leur consommation cloud en obtenant une estimation de la consommation et des frais à l’aide des API Usage et RateCard.

- **Prédiction de facture** : les clients et partenaires peuvent obtenir une estimation de leur consommation et de leur dépense cloud et appliquer des algorithmes d’apprentissage automatique pour prédire le montant de leur facture à la fin du cycle de facturation.

- **Analyse des coûts avant consommation** : les clients peuvent également utiliser l’API RateCard pour prédire le montant de leur facture s’ils choisissaient de déplacer leurs charges de travail vers Azure en fournissant les valeurs d’utilisation souhaitées. Si les clients disposent de charges de travail existantes dans d’autres clouds publics ou privés, ils peuvent également mapper leur utilisation sur les tarifs Azure afin d’obtenir une meilleure estimation de leur dépense Azure. Cette fonctionnalité offre une vue améliorée des informations qui peuvent être obtenues par le biais de la [Calculatrice de prix Azure](http://azure.microsoft.com/pricing/calculator/), étant donné (par exemple) que nos partenaires de facturation offrent la possibilité de s’axer sur une offre et de comparer/mettre en contraste différents types d’offre autres que Paiement à l’utilisation, notamment les offres Engagement monétaire et Crédit monétaire. Les API permettent également d’effectuer des modifications d’estimation de coûts par région, autorisant ainsi le type d’analyse de scénarios nécessaire pour la prise de décisions de déploiement, étant donné que le déploiement de ressources dans différents contrôleurs de domaine à travers le monde peut avoir un impact direct sur le coût total.

- **Analyse de scénarios** :

	- Les clients et partenaires peuvent déterminer s’il serait plus rentable pour eux d’exécuter leurs charges de travail dans une autre région ou dans une autre configuration de la ressource Azure. Les coûts des ressources Azure peuvent varier en fonction de la région Azure dans laquelle elles sont exécutées, ce qui permet aux clients et partenaires de bénéficier d’optimisations des coûts.

	- Les clients et partenaires peuvent également déterminer si un autre type d’offre Azure propose un meilleur tarif pour une ressource Azure.

## Solutions de partenaires

L’article [Cloudyn assure des tâches de gestion financière informatique pour ses clients grâce aux API Microsoft Azure Usage et RateCard](billing-usage-rate-card-partner-solution-cloudyn.md) décrit l’expérience d’intégration offerte par [Cloudyn](https://www.cloudyn.com/microsoft-azure/), partenaire sur les API Azure Billing. Cet article décrit ces expériences en détail et inclut notamment une courte vidéo expliquant la façon dont un client Azure peut utiliser Cloudyn et les API Azure Billing pour obtenir une vue d’ensemble de ses données de consommation Azure.

L’article [Intégration des API Microsoft Azure Billing par Cloud Cruiser](billing-usage-rate-card-partner-solution-cloudcruiser.md) décrit la façon dont la solution [Express de Cloud Cruiser pour Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) fonctionne directement à partir du portail WAP, permettant ainsi aux clients de gérer en toute transparence les aspects opérationnels et financiers de leur cloud public hébergé ou privé Microsoft Azure à partir d’une même interface utilisateur.

## Étapes suivantes
+ Pour plus d’informations sur ces deux API, qui font partie intégrante de l’ensemble d’API fourni par Azure Resource Manager, consultez la [Référence des API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).
+ Si vous souhaitez vous plonger directement dans l’exemple de code, consultez nos [exemples de code d’API Microsoft Azure Billing sur Github](https://github.com/Azure/BillingCodeSamples) (en anglais).

## En savoir plus
+ Pour plus d’informations sur Azure Resource Manager, voir l’article [Présentation d’Azure Resource Manager](resource-group-overview.md).
+ Pour plus d’informations sur la suite d’outils nécessaire pour acquérir une bonne compréhension de la dépense cloud, voir l’article du cabinet Gartner [Guide de marché relatif aux outils de gestion financière informatique (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb) (en anglais).

<!---HONumber=July15_HO3-->