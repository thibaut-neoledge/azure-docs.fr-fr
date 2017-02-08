---
title: "Contrôle d’accès, rôles et ressources dans Application Insights"
description: "Propriétaires, collaborateurs et lecteurs des perspectives de votre organisation."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/07/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1520fde6b60546e408772e04488e8a530a9c1344


---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Contrôle d’accès, rôles et ressources dans Application Insights
Vous pouvez contrôler qui a lu et mis à jour l’accès à vos données dans Visual Studio [Application Insights][start] à l’aide du [Contrôle d’accès basé sur les rôles dans Microsoft Azure](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Accordez l’accès aux utilisateurs dans le **groupe de ressources ou l’abonnement** auquel appartient votre ressource d’application et non dans la ressource elle-même. Affectez le rôle de **collaborateur de composants Application Insights** . Cela garantit un contrôle d’accès uniforme aux tests et aux alertes Web, ainsi qu’aux ressources de votre application. [En savoir plus](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Ressources, groupes et abonnements
Quelques définitions pour commencer :

* **Ressource** : une instance d’un service Microsoft Azure. Votre ressource Application Insights collecte, analyse et affiche les données de télémétrie envoyées par votre application.  Les autres types de ressources Azure incluent des applications Web, des bases de données et des machines virtuelles.
  
    Pour afficher toutes vos ressources, accédez au [portail Azure][portal], connectez-vous, puis cliquez sur Parcourir.
  
    ![Cliquez sur Parcourir, puis sur Tout ou Filtrer par Application Insights](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Groupe de ressources**][group] : chaque ressource appartient à un groupe. Un groupe est un moyen pratique de gérer les ressources apparentées, en particulier pour le contrôle d’accès. Par exemple, vous pouvez placer dans un groupe de ressources une application Web, une ressource Application Insights pour surveiller l’application et une ressource de stockage pour conserver les données exportées.

    ![Cliquez sur Parcourir, Groupes de ressources, puis choisissez un groupe](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Abonnement**](https://manage.windowsazure.com) : pour utiliser Application Insights ou d’autres ressources Azure, vous vous connectez à un abonnement Azure. Chaque groupe de ressources appartient à un abonnement Azure, où vous choisissez votre package de prix et, s’il s’agit d’un abonnement d’organisation, sélectionnez les membres et leurs autorisations d’accès.
* [**Compte Microsoft**][account] : le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter aux abonnements Microsoft Azure, XBox Live, Outlook.com et autres services Microsoft.

## <a name="a-nameaccessa-control-access-in-the-resource-group"></a><a name="access"></a> Contrôle de l’accès dans le groupe de ressources
Il est important de comprendre qu’en plus de la ressource que vous avez créée pour votre application, il existe également des ressources distinctes masquées pour les alertes et les tests Web. Elles sont associées au même [groupe de ressources](#resource-group) que votre application. Vous pouvez également placer d’autres services Azure ici, comme des sites Web ou du stockage.

![Ressources dans Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Pour contrôler l’accès à ces ressources, il est donc recommandé de :

* contrôler l’accès au niveau du **groupe de ressources ou de l’abonnement** .
* affecter le rôle de **collaborateur de composants Application Insights** . Cela leur permet de modifier les tests Web, les alertes et les ressources d’Application Insights, sans donner accès aux autres services dans le groupe.

## <a name="to-provide-access-to-another-user"></a>Pour fournir l’accès à un autre utilisateur
Vous devez disposer des droits du propriétaire de l’abonnement ou du groupe de ressources.

L’utilisateur doit avoir un [compte Microsoft][account] ou un accès à son [compte professionnel Microsoft](../active-directory/sign-up-organization.md). Vous pouvez fournir l’accès aux personnes et aux groupes d’utilisateurs définis dans Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Accédez au groupe de ressources
Ajoutez l’utilisateur à cet endroit.

![Dans le panneau de ressources de votre application, ouvrez Essentials, puis le groupe de ressources et sélectionnez Paramètres/Utilisateurs. Cliquez sur Ajouter.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Vous pouvez également monter d’un niveau supplémentaire et ajouter l’utilisateur à l’abonnement.

#### <a name="select-a-role"></a>Sélectionnez un rôle
![Sélectionnez un rôle pour le nouvel utilisateur](./media/app-insights-resources-roles-access-control/03-role.png)

| Rôle | Dans le groupe de ressources |
| --- | --- |
| Propriétaire |Peut tout modifier, y compris l’accès utilisateur |
| Collaborateur |Peut tout modifier, y compris l’ensemble des ressources |
| collaborateur de composants Application Insights |Peut modifier les ressources, les tests Web et les alertes Application Insights |
| Lecteur |Peut afficher les éléments, mais ne peut rien modifier |

Une « modification » inclut la création, la suppression et la mise à jour des éléments suivants :

* Ressources
* Tests web
* Alertes
* Exportation continue

#### <a name="select-the-user"></a>Sélectionnez l’utilisateur
![Tapez l’adresse e-mail d’un nouvel utilisateur. Sélectionnez l’utilisateur](./media/app-insights-resources-roles-access-control/04-user.png)

Si l’utilisateur n’est pas dans le répertoire, vous pouvez inviter toute personne disposant d’un compte Microsoft.
(Si elle utilise des services comme Outlook.com, OneDrive, Windows Phone ou XBox Live, elle dispose d’un compte Microsoft.)

## <a name="users-and-roles"></a>Utilisateurs et rôles
* [Contrôle d’accès en fonction du rôle dans Azure](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md



<!--HONumber=Nov16_HO3-->


