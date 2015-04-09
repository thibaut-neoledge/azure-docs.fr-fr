<properties 
	pageTitle="Contrôle du trafic des applications web Azure avec Azure Traffic Manager 
	description="Cet article présente des informations résumées sur Azure Traffic Manager, qui est associé aux applications web Azure. 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Contrôle du trafic des applications web Azure avec Azure Traffic Manager

> [AZURE.NOTE] Cet article présente des informations résumées sur Microsoft Azure Traffic Manager, qui est associé aux applications web Azure App Service. Vous trouverez d'autres informations sur Azure Traffic Manager en suivant les liens fournis à la fin de cet article.

## Introduction
Vous pouvez utiliser Azure Traffic Manager pour contrôler la façon dont les requêtes des clients web sont distribuées aux applications web dans Azure App Service. Lorsque des points de terminaison d'applications web sont ajoutés à un profil Azure Traffic Manager, ce dernier conserve une trace du statut de vos applications web (en cours d'exécution, arrêtées ou supprimées) pour pouvoir décider du point de terminaison qui doit recevoir le trafic.

## Méthodes d'équilibrage de charge
Azure Traffic Manager utilise trois méthodes d'équilibrage de charge. Elles sont décrites dans la liste suivante, car elles se rapportent aux applications web Azure. 

* **Basculement **: si vous avez des clones d'applications web dans différentes régions, vous pouvez utiliser cette méthode pour configurer une application web pour assurer la totalité du trafic du client web et en configurer une autre d'une région différente pour assurer ce trafic en cas de défaillance de la première application web. 
	
* **Tourniquet (round robin) **: si vous avez des clones d'applications web dans différentes régions, vous pouvez utiliser cette méthode pour répartir le trafic également entre les applications web des différentes régions. 
	
* **Performances **: la méthode de Performance distribue le trafic sur la base de l'aller-retour le plus court vers les clients. Cette méthode peut être utilisée pour les applications web d'une même région ou de régions différentes. 

Pour plus d'informations sur l'équilibrage de charge dans Azure Traffic Manager, voir [À propos des méthodes d'équilibrage de charge de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx).

##Applications web et profils dans Traffic Manager 
Pour configurer le contrôle du trafic des applications web, vous pouvez créer un profil dans Azure Traffic Manager, qui utilise l'une des trois méthodes d'équilibrage de charge décrites précédemment, puis ajouter au profil les points de terminaison (ici, des applications web) dont vous souhaitez contrôler le trafic. Le statut de votre application web (en cours d'exécution, arrêtée ou supprimée) est régulièrement communiqué au profil, ce qui permet à Azure Traffic Manager de diriger le trafic.

Lorsque vous utilisez Azure Traffic Manager avec Azure, tenez compte des points suivants :

* Pour les déploiements des applications web dans une même région uniquement, Web Apps fournit déjà les fonctionnalités de basculement et de tourniquet (round-robin) et ce, quel que soit le mode des applications web.

* Pour les déploiements dans une même région qui utilisent Web Apps conjointement avec un autre service cloud Azure, vous pouvez combiner les deux types de point de terminaison pour autoriser les scénarios hybrides.

* Vous pouvez spécifier un seul point de terminaison d'application web par région dans un profil. Lorsque vous sélectionnez une application web comme point de terminaison pour une région, les autres applications web de cette région ne peuvent plus être sélectionnées pour ce profil.

* Les points de terminaison des applications web que vous spécifiez dans un profil Azure Traffic Manager figurent dans la section **Noms de domaine** de la page de configuration des applications web du profil, mais ne peuvent pas être configurés ici.

* Lorsque vous avez ajouté une application web à un profil, l'**URL du site** dans le tableau de bord de la page du portail de l'application web affiche l'URL de domaine personnalisé de l'application web, le cas échéant. Sinon, elle affiche l'URL du profil Traffic Manager (par exemple,  `contoso.trafficmgr.com`). Le nom de domaine direct de l'application web et l'URL de Traffic Manager sont tous deux affichés dans la page de configuration de l'application web dans la section **Noms de domaine**.

* Vos noms de domaines personnalisés fonctionnent comme prévu, mais en plus de les ajouter à vos applications web, vous devez configurer votre carte DNS pour qu'elle pointe vers l'URL de Traffic Manager. Pour plus d'informations sur la configuration d'un domaine personnalisé pour une application web Azure, voir [Configuration d'un nom de domaine personnalisé pour un site web Azure](web-sites-custom-domain-name.md).

* Vous ne pouvez ajouter que des applications web en mode standard à un profil Azure Traffic Manager.

## Étapes suivantes

Pour une vue d'ensemble conceptuelle et technique d'Azure Traffic Manager, voir [Vue d'ensemble de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/hh744833.aspx). 

Pour plus d'informations sur la configuration d'Azure Traffic Manager, y compris pour l'utilisation de Web Apps, voir [Tâches de configuration de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/hh744830.aspx).

Pour plus d'informations sur l'équilibrage de charge dans Azure Traffic Manager, voir [À propos des méthodes d'équilibrage de charge de Traffic Manager](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx).

Pour plus d'informations sur l'utilisation de Traffic Manager avec Web Apps, voir les billets des blogs : 
[Utilisation d'Azure Traffic Manager avec les sites web Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) et [Azure Traffic Manager peut désormais s'intégrer aux sites web Azure](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

<!--HONumber=49-->