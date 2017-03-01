---
title: "Références sur la navigation dans le portail Azure"
description: "Découvrez les expériences utilisateur d&quot;App Service dans le portail de gestion et le portail Azure"
services: app-service
documentationcenter: 
author: jaime-espinosa
manager: erikre
editor: jimbe
ms.assetid: 0cc6a3cc-bd89-4a96-9177-d25f6fb737bb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: jaime-espinosa
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 00617f4d508c843000d69c02464a1bef36320a8e
ms.lasthandoff: 01/20/2017


---
# <a name="reference-for-navigating-the-azure-portal"></a>Références sur la navigation dans le portail Azure
Le service Sites Web Azure s’appelle désormais [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Nous sommes actuellement en train de mettre à jour l’ensemble de notre documentation afin qu’elle reflète ce changement de nom et fournisse les instructions adaptées au portail Azure. En attendant que ce processus soit terminé, vous pouvez utiliser ce document pour vous guider lors de l’utilisation de Web Apps dans le portail Azure.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="the-future-of-the-azure-classic-portal"></a>Ce qu’il adviendra du portail Azure Classic
Vous aurez peut-être déjà remarqué les modifications au niveau de la marque sur le portail Azure Classic. Celui-ci est en train d’être entièrement remplacé par le portail Azure. Le portail Azure Classic est en train d’être progressivement remplacé par le nouveau, et c’est sur ce dernier qu’a été déplacée la section concernant le nouveau développement. Toutes les nouvelles fonctionnalités de Web Apps arriveront avec le portail Azure. Commencez à utiliser le portail Azure pour bénéficier des toutes dernières fonctionnalités de Web Apps.

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Différences de disposition entre le portail Azure Classic et le portail Azure
Dans le portail Azure Classic, tous les services Azure étaient répertoriés sur le côté gauche. Le portail Azure Classic possédait une structure en arborescence, depuis laquelle vous démarriez à partir du service et naviguiez dans chaque élément. Cette structure fonctionne bien lorsque vous gérez des composants indépendants. Toutefois, les applications créées à partir d'Azure sont constituées de services interconnectés, et cette arborescence n'est pas idéale pour les collections de services. 

Le nouveau portail Azure facilite la création d’applications de bout en bout avec des composants provenant de plusieurs services. Le portail est organisé en *voyages*. Un *voyage* est un ensemble de *panneaux*, qui sont les conteneurs de différents composants. Par exemple, la configuration de la mise à l’échelle automatique pour une application web est un *voyage* qui vous mène à travers plusieurs panneaux, comme le montre l’exemple suivant : le panneau **site web** (le titre de ce panneau n’a pas encore été adapté à la nouvelle terminologie), le panneau **Paramètres** et le panneau **Montée en charge**. Dans cet exemple, la mise à l’échelle automatique est configurée pour dépendre de l’utilisation du processeur. Le panneau **Pourcentage UC** s’affiche donc également. Les composants des *panneaux* sont appelés des *portions* et ont l’aspect de mosaïques. 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>Exemple de navigation : créer une application web
La création d'applications web est toujours aussi facile qu'avant. L’illustration suivante montre le portail Azure Classic et le portail Azure côte à côte pour montrer que rien n’a changé dans le nombre d’étapes nécessaires à la création et à l’exécution d’applications web. 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

Dans le portail, vous pouvez choisir parmi les types d'applications web les plus courants, comme les applications de galerie telles que WordPress. Pour obtenir une liste complète des applications disponibles, rendez-vous sur [Azure Marketplace].

Quand vous créez une application web, vous spécifiez l’URL, le plan App Service et l’emplacement dans le portail, comme vous le faisiez dans le portail Azure Classic. 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

De plus, le portail permet de définir d'autres paramètres courants. Par exemple, les [groupes de ressources](../azure-resource-manager/resource-group-overview.md) facilitent l’affichage et la gestion des ressources Azure connexes. 

## <a name="navigation-example-settings-and-features"></a>Exemple de navigation : paramètres et fonctionnalités
Tous les paramètres et fonctionnalités sont désormais regroupés logiquement dans un même panneau, depuis lequel vous pouvez naviguer.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Par exemple, vous pouvez créer des domaines personnalisés en cliquant sur **Domaines personnalisés et SSL** dans le panneau **Paramètres**.

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Pour définir une alerte de surveillance, cliquez sur **Demandes et erreurs**, puis sur **Ajouter une alerte**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Pour activer les diagnostics, cliquez sur **Journaux de diagnostic** dans le panneau **Paramètres**.

![](./media/app-service-web-app-azure-portal/Diagnostics.png)

Pour configurer les paramètres de l’application, cliquez sur **Paramètres de l’application** dans le panneau **Paramètres**. 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Outre le nom de la marque, quelques autres éléments du portail ont été renommés ou regroupés différemment pour faciliter leur recherche. Par exemple, voici une capture d’écran de la page correspondante des paramètres d’application (**Configurer**) dans le portail Classic.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>Autres ressources
[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## <a name="whats-changed"></a>Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)


