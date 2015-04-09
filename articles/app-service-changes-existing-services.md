<properties 
	pageTitle="Azure App Service et son impact sur les services Azure existants" 
	description="Explique l'impact d'Azure App Service et de ses fonctionnalités sur les services existants d'Azure." 
	authors="yochayk" 
	writer="yochayk" 
	editor="yochayk" 
	manager="nirma" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="yochayk"/>


# Azure App Service et les services Azure existants

Cet article présente les modifications apportées aux services Azure existants dans le cadre du regroupement de plusieurs services Azure au sein d'[Azure App Service](http://azure.microsoft.com/services/app-service/).

## Présentation 

[Azure App Service](http://azure.microsoft.com/services/app-service/) est un nouveau service cloud qui permet aux développeurs de créer des applications web et mobiles pour toutes les plateformes et tous les appareils. App Service est une solution intégrée conçue pour simplifier les fonctions de codage répétées. Elle s'intègre aux systèmes entreprise et SaaS, et automatise les processus métier tout en répondant à vos besoins en matière de sécurité, de fiabilité et d'évolutivité.

App Service réunit les services Azure existants [Sites Web](http://azure.microsoft.com/services/websites/), [Mobile Services](http://azure.microsoft.com/services/mobile-services/) et [Biztalk Services](http://azure.microsoft.com/services/biztalk-services/) en un seul et même service, tout en y ajoutant de nouvelles fonctionnalités puissantes.  App Service vous permet d'héberger les types d'applications suivants : 

-   Applications web (Web Apps)
-   Applications mobiles (Mobile Apps)
-   Applications API (API Apps)
-   Applications logiques (Logic Apps)

Le tableau suivant montre la correspondance entre les services Azure existants et App Service, ainsi que les types d'applications qu'ils contiennent.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Service Azure existant</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">Ce qui a changé</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sites Web Azure</td>
<td align="left">Web Apps</td>
<td align="left"><li>Pour Sites Web Azure, le changement concerne uniquement le remplacement du nom  Sites Web remplacé par Web Apps.
<p><li>Toutes vos instances existantes de Sites Web s'appellent désormais Web Apps dans App Service.</p>
<p><li>Vous pouvez accéder à vos sites Web existants via le <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portail Azure</a>, où vous trouverez tous vos sites existants sous <em>Web Apps</em>.</p>
<p><li><em>Le plan d'hébergement web</em> s'appelle désormais <em>plan App Service</em>. Un <em>plan App Service</em> peut héberger n'importe quel type d'application App Service (Web Apps, Mobile Apps, Logic Apps ou API Apps).</p>
<p><li>Azure App Service Web Apps se trouve sous Disponibilité générale.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">En savoir plus sur Web Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Azure Mobile Services</td>
<td align="left">Mobile Apps</td>
<td align="left"><p><li>Mobile Services est toujours disponible en tant que service autonome et reste entièrement pris en charge.</p>
<p><li>Mobile Apps est un nouveau type d'application d'App Service qui intègre toutes les fonctionnalités de Mobile Services et plus encore. Mobile Apps est en version préliminaire publique.</p>
<p><li>La migration <a href="http://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview/">de Mobile Services à Mobile Apps est facile</a>. Étant donné que Mobile Apps est encore en version préliminaire, il n'est pas recommandé de l'utiliser avec des applications de production.</p>
<p><li>Dans App Service, Mobile Apps améliore les fonctionnalités de Mobile Services, telles que  l'intégration aux systèmes locaux et SaaS, les emplacements intermédiaires, les tâches web, de meilleures options de mise à l'échelle, et bien plus encore.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">En savoir plus sur Mobile Apps</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API Apps</td>
<td align="left">
<p><li>API Apps est un nouveau type d'application d'App Service qui permet de créer et d'utiliser des API dans le cloud.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">En savoir plus sur API Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logic Apps</td>
<td align="left">
<p><li>Logic Apps est un nouveau type d'application d'App Service qui permet d'automatiser les processus d'entreprise.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">En savoir plus sur Logic Apps</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk Services</td>
<td align="left">BizTalk API Apps</td>
<td align="left">
<li><p>BizTalk Services est toujours disponible en tant que service autonome et reste entièrement pris en charge.</p>
<li><p>Toutes les fonctionnalités de BizTalk Services sont intégrées à App Service en tant qu'applications API Apps permettant aux utilisateurs d'intégrer des applications d'entreprise et d'effectuer des intégrations B2B avec n'importe quel type d'application dans App Service</p>
<li><p>Avec Logic Apps, vous pouvez désormais automatiser les processus d'entreprise grâce à une expérience de conception visuelle permettant de créer des flux de travail</p></td>
</tr>
</tbody>
</table>

Pour plus d'informations, consultez la [documentation App Service](http://azure.microsoft.com/documentation/services/app-service/).


<!--HONumber=49-->