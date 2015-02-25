<properties 
	pageTitle="Contrôle du trafic du service Sites Web Azure avec Azure Traffic Manager" 
	description="Cet article présente des informations succinctes sur Microsoft Azure Traffic Manager, qui est associé à Sites Web Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Contrôle du trafic du service Sites Web Azure avec Azure Traffic Manager

> [AZURE.NOTE] Cet article présente des informations succinctes sur Microsoft Azure Traffic Manager, qui est associé à Sites Web Azure. Vous trouverez d'autres informations sur Azure Traffic Manager en suivant les liens fournis à la fin de cet article.

## Introduction
Vous pouvez utiliser Azure Traffic Manager pour contrôler la distribution des demandes des clients Web sur le service Sites Web Azure. Lorsque des points de terminaison de sites Web Azure sont ajoutés à un profil Azure Traffic Manager, ce dernier conserve une trace du statut de vos sites Web (en cours d'exécution, arrêté ou supprimé) pour pouvoir décider lequel de ces points de terminaison doit recevoir le trafic.

## Méthodes d'équilibrage de charge
Azure Traffic Manager utilise trois méthodes d'équilibrage de charge. Elles sont décrites dans la liste suivante, car elles se rapportent au service Sites Web Azure. 

* **Basculement** : si vous avez des clones de sites Web dans différentes régions, vous pouvez utiliser cette méthode pour configurer un site Web pour assurer la totalité du trafic du client Web et en configurer un autre d'une autre région pour assurer ce trafic en cas de défaillance du premier site Web. 
	
* **Tourniquet** : si vous avez des clones de sites Web dans différentes régions, vous pouvez utiliser cette méthode pour distribuer le trafic à parts égales sur les sites Web des différentes régions. 
	
* **Performance** : la méthode de Performance distribue le trafic sur la base de l'aller-retour le plus court vers les clients. Cette méthode peut être utilisée pour les sites Web d'une même région ou de régions différentes. 

Pour plus d'informations sur l'équilibrage de charge dans Azure Traffic Manager, consultez la rubrique [À propos des méthodes d'équilibrage de charge dans Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn339010.aspx).

##Sites Web Azure et profils Traffic Manager 
Pour configurer le contrôle du trafic des sites Web, vous pouvez créer un profil dans Azure Traffic Manager, qui utilise l'une des trois méthodes d'équilibrage de charge décrites précédemment, puis ajouter les points de terminaison (ici, des sites Web) dont vous souhaitez contrôler le trafic sur le profil. Le statut de votre site Web (en cours d'exécution, arrêté ou supprimé) est régulièrement communiqué au profil, ce qui permet à Azure Traffic Manager de diriger le trafic.

Lorsque vous utilisez Azure Traffic Manager avec Azure, tenez compte des points suivants :

* Pour les déploiements de sites Web uniquement dans la même région, Sites Web Azure fournit déjà les fonctionnalités de basculement et de tourniquet indépendamment du mode du site Web.

* Pour les déploiements dans la même région qui utilisent Sites Web Azure conjointement avec un autre service cloud Azure, vous pouvez combiner les types de points de terminaison pour autoriser les scénarios hybrides.

* Vous pouvez spécifier un seul point de terminaison de site Web par région dans un profil. Lorsque vous sélectionnez un site Web comme point de terminaison pour une région, les autres sites Web de cette région ne peuvent plus être sélectionnés pour ce profil.

* Les points de terminaison de site Web que vous spécifiez dans un profil Azure Traffic Manager figurent dans la section **Noms de domaine** dans la page de configuration des sites Web du profil, mais ne peuvent pas être configurés ici.

* Quand vous avez ajouté un site Web à un profil, l'**URL du site** dans le tableau de bord de la page du portail du site Web affiche l'URL de domaine personnalisé du site Web, le cas échéant. Sinon, elle affiche l'URL du profil Traffic Manager (par exemple, `contoso.trafficmgr.com`). Le nom de domaine direct du site Web et l'URL Traffic Manager sont tous deux affichés dans la page de configuration du site Web dans la section **Noms de domaine**.

* Vos noms de domaine personnalisés fonctionnent comme prévu mais, en plus de les ajouter à vos sites Web, vous devez configurer votre carte DNS pour qu'elle pointe sur l'URL Traffic Manager. Pour plus d'informations sur la configuration d'un domaine personnalisé pour un site Web Azure, consultez la page [Configuration d'un nom de domaine personnalisé pour un site Web Azure](https://www.windowsazure.com/fr-fr/documentation/articles/web-sites-custom-domain-name/).

* Vous pouvez uniquement ajouter des sites Web qui sont en mode Standard dans un profil Azure Traffic Manager.

## Étapes suivantes

Pour une vue d'ensemble conceptuelle et technique d'Azure Traffic Manager, consultez la rubrique [Vue d'ensemble de Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx). 

Pour plus d'informations sur la configuration d'Azure Traffic Manager, y compris pour l'utilisation de Sites Web Azure, consultez la rubrique [Tâches de configuration Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744830.aspx).

Pour plus d'informations sur l'équilibrage de charge dans Azure Traffic Manager, consultez la rubrique [À propos des méthodes d'équilibrage de charge dans Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn339010.aspx).

Pour plus d'informations sur l'utilisation de Traffic Manager avec Sites Web Azure, consultez les billets de blog 
[Utilisation d'Azure Traffic Manager avec Sites Web Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) et [Azure Traffic Manager peut désormais être intégré à Sites Web Azure](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).


<!--HONumber=42-->
