<properties 
	pageTitle="Présentation détaillée des plans Azure App Service" 
	description="Découvrez comment fonctionnent les plans Azure App Service et comment ils peuvent améliorer votre gestion." 
	keywords="app service, azure app service, mise à l'échelle, évolutif, plan app service, coût d'app service"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2016" 
	ms.author="byvinyal"/>

#Présentation détaillée des plans Azure App Service#

Un **plan App Service** représente un ensemble de fonctionnalités et de capacités que vous pouvez partager entre plusieurs applications dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), y compris Web Apps, Mobile Apps, Logic Apps et API Apps. Ces plans prennent en charge 5 niveaux de tarification (**Gratuit**, **Partagé**, **De base**, **Standard** et **Premium**) qui possèdent chacun leurs propres fonctionnalités et capacités. Les applications comprises dans un même abonnement et situées au même emplacement peuvent partager un plan. Toutes les applications qui partagent un plan peuvent tirer profit de l'ensemble des fonctionnalités définies par ce plan. Toutes les applications web associées à un plan donné utilisent les ressources définies par ce plan. Par exemple, si votre plan est configuré pour utiliser deux « petites » instances d'un niveau de service standard, toutes les applications associées à ce plan seront exécutées sur ces deux instances et auront accès aux fonctionnalités du niveau de service standard. Les instances de plans sur lesquelles sont exécutées des applications sont entièrement gérées et hautement disponibles.

Dans cet article, nous allons explorer les principales caractéristiques telles que le niveau et l'échelle d'un plan App  Service, et comment elles entrent en jeu lors de la gestion de vos applications.

##Applications et plans App Service

Une application App Service ne peut être associée qu'à un seul plan App Service à la fois.

Les applications et les plans sont contenus dans un groupe de ressources. Un groupe de ressources sert de limite de cycle de vie pour chaque ressource qu'il contient. Les groupes de ressources permettent de gérer toutes les parties d'une application en même temps.

La possibilité d'avoir plusieurs plans App Service dans un même groupe de ressources vous permet d'allouer différentes applications à différentes ressources physiques. Par exemple, cela permet de distinguer les ressources des environnements de développement, de test et de production. Vous pourriez, par exemple, avoir besoin d'allouer un plan à son ensemble de ressources dédiées pour vos applications de production, et un second plan pour vos environnements de développement et de test. De cette façon, les tests de charge d'une nouvelle version de vos applications ne feront pas appel aux mêmes ressources que les applications de production qui sont distribuées aux clients réels.

Avoir plusieurs plans au sein d'un même groupe de ressources permet également de définir une application disponible pour plusieurs régions géographiques. Par exemple, une application hautement disponible qui s'exécute dans deux régions inclut deux plans, un pour chaque région, et une application associée à chaque plan. Dans ce cas, toutes les copies de l'application seront associées à un seul groupe de ressources. Le fait de disposer d'une vue unique d'un groupe de ressources avec plusieurs plans et plusieurs applications facilite la gestion, le contrôle et l'affichage de l'intégrité de l'application.

## Création d'un plan App Service contre utilisation d'un plan existant

Quand vous créez une application, vous devez envisager de créer un groupe de ressources quand l'application qui va être créée correspond à un tout nouveau projet. Dans ce cas, la bonne option consiste à créer un groupe de ressources, un plan et une application.

Si l'application que vous allez créer est un composant d'une autre application, elle doit être créée au sein du groupe de ressources alloué pour l'application en question.

Que la nouvelle application soit autonome ou fasse partie d'une autre application, vous pouvez choisir d'utiliser un plan App Service existant pour l'héberger ou bien en créer un. C'est plus une question de capacité et de charge attendue. Si la nouvelle application consommera beaucoup de ressources et aura des facteurs d'échelle différents des autres applications hébergées dans un plan existant, il est recommandé de l'isoler dans son propre plan.

La création d'un plan permet d'allouer un nouvel ensemble de ressources aux applications web et de mieux contrôler l'allocation de ressources, car chaque plan obtient son propre ensemble d'instances.
 
La capacité à déplacer des applications d'un plan à un autre permet également de modifier la façon dont les ressources sont allouées dans toute l'application principale.
 
Si vous souhaitez créer une application dans une autre région et que celle-ci n'a pas de plan existant, vous devrez en créer un dans cette région pour pouvoir y héberger votre application.

## Créer un plan App Service

Vous pouvez créer un **plan App Service** vide à partir de l’expérience de navigation du **plan App Service** ou dans le cadre de la création d'applications.

Pour ce faire, cliquez dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), sur **Nouveau**, sélectionnez **Web + mobile**, puis **Web Apps**, **Mobile Apps**, **Logic Apps** ou **API Apps**. ![][createWebApp]

Vous pouvez ensuite sélectionner ou créer le plan App Service pour la nouvelle application.
  
 ![][createASP]

Pour créer un nouveau plan App Service, cliquez sur **+ Créer nouveau**, saisissez le nom du **plan App Service**, puis sélectionnez un **emplacement**. Cliquez sur **Niveau de tarification**, puis sélectionnez un niveau de tarification approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d'options de tarification, telles que **Gratuit** et **Partagé**. Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner**.
 
## Déplacer une application vers un autre plan App Service

Vous pouvez déplacer une application vers un autre plan App Service depuis le [portail Azure](https://portal.azure.com). Les applications peuvent être déplacées d’un plan à l’autre tant que les plans se trouvent dans le même groupe de ressources et dans la même région géographique.

Pour déplacer une application vers un autre plan, accédez à l’application que vous souhaitez déplacer. Dans le menu Paramètres, recherchez **Modifier le plan App Service**.
 
Cette action ouvre le sélecteur de plan App Service. À ce stade, vous pouvez choisir un plan existant ou en créer un. Seuls les plans valides (dans le même groupe de ressources et le même emplacement géographique) s’affichent.

![][change]

Notez que chaque plan a son propre niveau de tarification. Par exemple, quand vous faites passer un site du niveau **Gratuit** au niveau **Standard**, votre application peut tirer parti de toutes les fonctionnalités et ressources du niveau **Standard**.

## Cloner une application vers un autre plan App Service
Si vous souhaitez déplacer l'application vers une autre région, vous pouvez également utiliser le clonage d’application. Le clonage crée une copie de votre application dans un plan ou un environnement App Service, nouveau ou existant, dans n'importe quelle région.

 ![][appclone]
 
Vous pouvez trouver **Cloner une application** dans le menu **Outils**.

Le clonage a certaines limitations : vous pouvez en savoir plus à ce sujet [ici](../app-service-web/app-service-web-app-cloning-portal.md)

## Mettre à l’échelle un plan App Service

Il existe trois façons de mettre à l'échelle un plan :

- Modifier le **niveau de tarification** du plan. Par exemple, un plan associé au niveau de tarification **De base** peut être converti en un plan de niveau **Standard** ou **Premium**. Toutes les applications associées à ce plan peuvent alors utiliser les fonctionnalités proposées par le nouveau niveau de service.
- Modifier la **taille des instances** du plan. Par exemple, un plan associé au niveau de tarification **De base** et utilisant des **petites** instances peut être modifié pour utiliser de **grandes** instances. Toutes les applications associées à ce plan pourront tirer parti de la mémoire supplémentaire et des ressources processeur offertes par l'instance plus grande.
- Modifier le **nombre d’instances** du plan. Par exemple, un plan **Standard** comprenant 3 instances peut voir son nombre d’instances monter jusqu’à 10. Un plan **Premium** peut voir son nombre d’instances monter jusqu’à 20 (en fonction de la disponibilité). Toutes les applications associées à ce plan pourront tirer parti de la mémoire supplémentaire et des ressources processeur permises par le nombre supérieur d'instances.

Vous pouvez modifier le niveau tarifaire et la taille de l’instance en cliquant sur l’élément **Montée en puissance** sous les paramètres du plan de l'application ou du plan App Service. Les modifications s’appliqueront au **Plan App Service** et affecteront toutes les applications hébergées par celui-ci.
 
 ![][pricingtier]

##Résumé

Les plans App Service représentent un ensemble de fonctionnalités et de capacités que vous pouvez partager entre vos différentes applications. Les plans App Service vous donnent la possibilité d'allouer des applications spécifiques à un ensemble donné de ressources, et d'optimiser davantage l'utilisation des ressources Azure. Ainsi, si vous souhaitez faire des économies sur votre environnement de test, vous pouvez partager un même plan entre plusieurs applications. Vous pouvez également augmenter le débit de votre environnement de production en le mettant à l'échelle dans plusieurs régions et plusieurs plans.

## Changements apportés

* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
   
[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png

<!---HONumber=AcomDC_0518_2016-->