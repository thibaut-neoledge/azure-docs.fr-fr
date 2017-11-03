---
title: Afficher le trafic par Traffic View dans Azure Traffic Manager | Microsoft Docs
description: "Introduction à la fonctionnalité d’affichage de trafic Traffic View et à Traffic Manager"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 0a07ff578c6afeedc6f3806d52bfe5aef6945c04
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-traffic-view"></a>Traffic View de Traffic Manager

>[!NOTE]
>La fonctionnalité d’affichage de trafic Traffic View dans Traffic Manager est en préversion publique. Il se peut qu’elle n’offre pas les mêmes niveaux de disponibilité et de fiabilité que les fonctions de la version mise à la disposition générale. Cette fonctionnalité n’est pas prise en charge, est susceptible de disposer de possibilités limitées et peut ne pas être disponible à certains emplacements Azure. Pour les notifications les plus récentes sur la disponibilité et l’état de cette fonctionnalité, consultez la page relative aux [mises à jour d’Azure Traffic Manager](https://azure.microsoft.com/updates/?product=traffic-manager).

Traffic Manager vous offre un routage au niveau DNS, afin que vos utilisateurs finals soient dirigés vers les points de terminaison sains, en fonction de la méthode de routage que vous aviez configurée au moment de la création du profil. Traffic Manager se trouve ainsi pourvu d’une vue de vos bases d’utilisateurs (avec une granularité au niveau de la résolution DNS) et de leur schéma de trafic. Lorsque vous activez Traffic View, ces informations sont traitées pour vous fournir des renseignements exploitables. 

Grâce à Traffic View, vous pouvez :
- comprendre où se trouvent vos bases d’utilisateurs (jusqu’à une granularité fixée au niveau de la résolution DNS locale)
- voir le volume de trafic (observé pour les requêtes DNS gérées par Azure Traffic Manager) provenant de ces régions.
-  obtenir des insights sur la latence représentative expérimentée par ces utilisateurs.
- explorez en profondeur les modèles de trafic spécifiques à partir de chacune de ces bases d’utilisateurs vers les régions Azure où vous avez des points de terminaison. 

Par exemple, vous pouvez utiliser Traffic View pour comprendre quelles régions enregistrent des volumes de trafic importants, mais pâtissent de latences supérieures. Vous pouvez ensuite utiliser ces informations pour planifier votre phase d’expansion sur de nouvelles régions Azure, afin que ces utilisateurs puissent bénéficier d’une latence plus faible.

## <a name="how-traffic-view-works"></a>Fonctionnement de Traffic View

Le fonctionnement de Traffic View est alimenté par l’observation que Traffic Manager effectue sur les requêtes entrantes, reçues au cours des sept derniers jours sur un profil pour lequel cette fonctionnalité a été activée. À partir de ces informations, l’adresse IP source est extraite de la résolution DNS qui est alors utilisée comme représentation de l’emplacement des utilisateurs. Ceux-ci sont alors regroupés ensemble, selon une granularité fixée au niveau de la résolution DNS, afin de créer des régions de bases d’utilisateurs en utilisant les informations géographiques des adresses IP gérées par Traffic Manager. Traffic Manager examine ensuite les régions Azure vers lesquelles la requête a été routée, et constitue une carte des flux de trafic pour les utilisateurs à partir de ces régions.
À l’étape suivante, Traffic Manager met en corrélation la région des bases d’utilisateurs avec le mappage des régions Azure au moyen des tables de latence de l’intelligence réseau qu’il tient à jour, pour que les différents réseaux d’utilisateurs finals comprennent la latence moyenne rencontrée par les utilisateurs de ces régions lorsqu’ils se connectent à des régions Azure. Tous ces calculs sont ensuite synthétisés sous forme de tableaux au niveau de l’adresse IP de la résolution DNS locale avant de vous être présentés. Vous pouvez traiter ces informations dans le workflow d’analyse de votre choix, et avez également la possibilité de télécharger ces données sous la forme d’un fichier CSV.
Lorsque vous utilisez Traffic View, vous êtes facturé en fonction du nombre de points de données utilisés pour créer les insights présentés. Actuellement, le seul type de point de données utilisé correspond aux requêtes reçues par rapport à votre profil Traffic Manager. Pour plus d’informations sur les prix, visitez la page [Tarifs Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-overview.md)
- En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager
- En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-create-profile.md)

