<properties
	pageTitle="Références sur la navigation dans le portail Azure"
	description="Découvrez les expériences utilisateur d'App Service dans le portail de gestion et le portail Azure"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="jaime-espinosa"/>

# Références sur la navigation dans le portail Azure

Le service Sites Web Azure s'appelle désormais [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Nous allons mettre à jour toute notre documentation pour refléter ce changement de nom et fournir des instructions concernant le nouveau portail Azure. En attendant que ce processus soit terminé, vous pouvez utiliser ce document pour vous guider lors de l'utilisation de Web Apps dans le nouveau portail Azure.
 
## Ce qu'il adviendra de l'ancien portail

Vous aurez peut-être déjà remarqué les modifications au niveau de la marque sur l'ancien portail. Celui-ci est en train d'être entièrement remplacé par le nouveau portail Azure. L'ancien portail est en train d'être progressivement remplacé par le nouveau, et c'est sur ce dernier qu'a été déplacée la section concernant le nouveau développement. Toutes les nouvelles fonctionnalités de Web Apps arriveront avec le nouveau portail. Commencez à utiliser le nouveau portail pour bénéficier des toutes dernières fonctionnalités de Web Apps.

## Différences de disposition entre l'ancien et le nouveau portail Azure

Dans l'ancien portail, tous les services Azure étaient répertoriés sur le côté gauche. L'ancien portail possédait une structure en arborescence, depuis laquelle vous démarriez à partir du service et naviguiez dans chaque élément. Cette structure fonctionne bien lorsque vous gérez des composants indépendants. Toutefois, les applications créées à partir d'Azure sont constituées de services interconnectés, et cette arborescence n'est pas idéale pour les collections de services. 

Le nouveau portail facilite la création d'applications de bout en bout avec des composants provenant de plusieurs services. Le nouveau portail Azure est organisé en  *journeys*. Un  *journey* est un ensemble de  *blades*, qui sont les conteneurs de différents composants. Par exemple, la configuration de la mise à l'échelle automatique pour une application web est un  *journey* qui vous mène à travers plusieurs panneaux, comme le montre l'exemple suivant : le panneau **Site web** (le titre de ce panneau n'a pas encore été adapté à la nouvelle terminologie), le panneau **Paramètres** et le panneau **Échelle**. Dans cet exemple, la mise à l'échelle automatique est configurée pour dépendre de l'utilisation du processeur. Le panneau **Pourcentage UC** s'affiche donc également. Les composants des  *blades* sont appelés des  *parts*, et ont l'aspect de mosaïques. 

![](./media/app-service-web-app-preview-portal/AutoScaling.png)

## Exemple de navigation : créer une application web

La création d'applications web est toujours aussi facile qu'avant. L'illustration suivante montre l'ancien portail et le nouveau portail côte à côte pour montrer que rien n'a changé dans le nombre d'étapes nécessaires à la création et à l'exécution d'applications. 

![](./media/app-service-web-app-preview-portal/CreateWebApp.png)

Dans le nouveau portail, vous pouvez choisir parmi les types d'applications web les plus courants, comme les applications de galerie telles que WordPress. Pour obtenir une liste complète des applications disponibles, rendez-vous sur [Azure Marketplace].

Quand vous créez une application web, vous spécifiez l'URL, le plan App Service et l'emplacement dans le nouveau portail, comme vous le faisiez dans l'ancien portail. 

![](./media/app-service-web-app-preview-portal/CreateWebAppSettings.png)

De plus, le nouveau portail permet de définir d'autres paramètres courants. Par exemple, les [groupes de ressources](azure-preview-portal-using-resource-groups.md) facilitent l'affichage et la gestion des ressources Azure connexes. 

## Exemple de navigation : paramètres et fonctionnalités

Tous les paramètres et fonctionnalités sont désormais regroupés logiquement dans un même panneau, depuis lequel vous pouvez naviguer.

![](./media/app-service-web-app-preview-portal/WebAppSettings.png)

Par exemple, vous pouvez créer des domaines personnalisés en cliquant sur **Domaines personnalisés et SSL** dans le panneau **Paramètres**.

![](./media/app-service-web-app-preview-portal/ConfigureWebApp.png)

Pour définir une alerte de surveillance, cliquez sur **Demandes et erreurs**, puis sur **Ajouter une alerte**.

![](./media/app-service-web-app-preview-portal/Monitoring.png)

Pour activer les diagnostics, cliquez sur **Journaux de diagnostic** dans le panneau **Paramètres**.

![](./media/app-service-web-app-preview-portal/Diagnostics.png)
 
Pour configurer les paramètres de l'application, cliquez sur **Paramètres de l'application** dans le panneau **Paramètres**. 

![](./media/app-service-web-app-preview-portal/AppSettingsPreview.png)

Outre le nom de la marque, quelques autres éléments du portail ont été renommés ou regroupés différemment pour faciliter leur recherche. Par exemple, voici une capture d'écran de la page correspondante des paramètres de l'application (**Configurer**) dans l'ancien portail.

![](./media/app-service-web-app-preview-portal/AppSettings.png)

## Autres ressources

[Plateforme d'applications cloud Azure](app-service-cloud-app-platform.md)

[Nouveau portail]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Si vous voulez commencer à utiliser Azure App Service avant de créer un compte Azure, cliquez sur [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez alors créer instantanément une application web de démarrage dans App Service. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

## Nouveautés
* Pour savoir comment passer de Sites Web à App Service, consultez : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour savoir comment passer de l'ancien au nouveau portail, consultez : [Références sur la navigation dans le nouveau portail](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->