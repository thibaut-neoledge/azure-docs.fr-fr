<properties 
	pageTitle="Présentation des plans d'hébergement web" 
	description="Surveillance des plans d'hébergement web" 
	services="web-sites" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

# Présentation des plans d'hébergement web #

Les plans d'hébergement web représentent un ensemble de fonctionnalités et de capacités que vous pouvez partager sur vos sites web.  Les plans d'hébergement web prennent en charge plusieurs options de tarification (Gratuit, Partagé, De base et Standard), chacune ayant ses propres possibilités.  Les sites des mêmes abonnement, groupe de ressources et emplacement géographique peuvent partager un plan d'hébergement web.  Vous pouvez rapidement changer le plan d'hébergement d'un site sans interruption, sous réserve que le plan d'hébergement cible soit présent au sein du même groupe de ressources et au même emplacement géographique.

## Fonctionnalités des plans d'hébergement web ##

Chaque niveau de tarification (Gratuit, Partagé, De base et Standard) a ses propres fonctionnalités.  [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=394421) pour connaître les fonctionnalités et les informations de tarification les plus récentes.

Voici quelques conseils concernant les plans d'hébergement web et les fonctionnalités :

- Vous pouvez modifier l'option de tarification d'un plan d'hébergement web quand vous le souhaitez, sans temps d'interruption.
- Pour optimiser les dépenses, les sites des mêmes abonnement, emplacement et groupe de ressources peuvent partager un plan d'hébergement web. 
- Les sites des mêmes abonnement, emplacement et groupe de ressources peuvent partager un plan d'hébergement web. 
- Vous pouvez modifier le plan d'hébergement web des sites et emplacements de déploiement sans aucune interruption, sous réserve que le plan d'hébergement cible soit présent au sein du même groupe de ressources que le plan d'origine.
- Les fonctionnalités comme la mise à l'échelle automatique ciblent un plan d'hébergement web.  Pour mettre à l'échelle automatiquement des sites individuels, dédiez un plan d'hébergement web à chaque site.

[En savoir plus sur la gestion des plans d'hébergement web](http://go.microsoft.com/fwlink/?LinkID=394411).

## Plans d'hébergement web et capacité ##

Les plans d'hébergement web des niveaux Gratuit et Partagé offrent aux sites une infrastructure partagée. Autrement dit, vos sites partagent des ressources avec les sites d'autres clients.  

Les plans d'hébergement web des niveaux De base et Standard fournissent aux sites une infrastructure dédiée. Autrement dit, seuls les sites que vous choisissez d'associer à ce plan sont exécutés sur ces ressources.  Avec ces options, vous pouvez configurer votre plan d'hébergement web pour utiliser une ou plusieurs instances de machine virtuelle.  Les instances sont toutes supportées quelle que soit leur taille (petites, moyennes ou grandes).  Ces machines virtuelles sont gérées pour vous, ce qui vous évite des préoccupations telles que les mises à jour du système d'exploitation.  

Remarque concernant le niveau Partagé (version préliminaire).  Pour toutes les options autres que  'Shared', le prix du plan d'hébergement web est basé sur l'option et la capacité choisies, sans frais supplémentaires pour chaque site qui utilise le plan. Les plans d'hébergement web de type Partagé sont différents.  Du fait de l'infrastructure partagée, chaque site du plan est facturé séparément.  

### Plans d'hébergement web et nouvelle version préliminaire du portail Azure ###

La nouvelle version préliminaire du portail Azure vous permet d'associer vos sites web, anciens ou nouveaux, à vos plans d'hébergement web.  En fait, tous les sites web anciens ont été automatiquement attribués aux plans d'hébergement web selon leur abonnement, leur emplacement géographique et l'option actuelle de tarification.  

Lorsque vous créez un site, le portail vous demande à quel plan d'hébergement web l'associer.  Vous pouvez créer un plan d'hébergement web ou en sélectionner un.  Pour utiliser un plan existant, votre nouveau site doit faire partie des mêmes abonnement, emplacement géographique et groupe de ressources que le plan.  Lorsque vous créez un site web vide, Azure l'attribue par défaut au dernier plan d'hébergement web utilisé pour optimiser l'utilisation des plans déjà créés.  Lorsque vous créez un site web avec une base de données, vous ne pouvez pas réutiliser un plan existant.

Vous pouvez consulter tous vos plans d'hébergement web pour tous vos abonnements en cliquant sur le bouton **Parcourir**, situé dans la barre de menus à gauche, puis en cliquant sur **Plans d'hébergement web**, situé en haut à droite du panneau d'activité affiché à l'écran.

![][BrowseEverythingScreenshot]

Vous pouvez voir à quel plan d'hébergement web est associé chaque site web en consultant la section Les indispensables, affichée en haut du panneau de votre site web.  Cliquez sur le plan pour ouvrir un panneau qui permet de gérer votre plan d'hébergement web.  [En savoir plus sur la gestion des plans d'hébergement web](http://go.microsoft.com/fwlink/?LinkID=394411).

![][WebsiteEssentials]

### Étapes suivantes ###

Pour prendre en main Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure](http://azure.microsoft.com/pricing/free-trial/).

<!-- Images. -->
[BrowseEverythingScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-everything.png
[WebsiteEssentials]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png




<!--HONumber=52-->