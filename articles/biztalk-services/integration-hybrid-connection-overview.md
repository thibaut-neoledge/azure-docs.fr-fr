<properties 
	pageTitle="Aperçu des connexions hybrides | Microsoft Azure" 
	description="Découvrez les connexions hybrides, y compris la sécurité, les ports TCP et les configurations prises en charge. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/14/2015" 
	ms.author="mandia"/>


# Aperçu des connexions hybrides
Cette rubrique présente les connexions hybrides et répertorie les configurations prises en charge ainsi que les ports TCP requis.


## Présentation des connexions hybrides

Les connexions hybrides sont une fonctionnalité d’Azure BizTalk Services : Les connexions hybrides sont un moyen simple et pratique de connecter des applications Azure Web Apps (anciennement Sites Web) et des applications Azure Mobile Apps (anciennement Mobile Services) à des ressources locales derrière votre pare-feu :

![Connexions hybrides][HCImage]

Les avantages des connexions hybrides sont les suivants :

- Les applications Web Apps et Mobile Apps peuvent accéder aux données et aux services locaux existants en toute sécurité.
- Plusieurs applications Web Apps et Mobile Apps peuvent partager une connexion hybride pour accéder à une ressource locale. 
- Un nombre minimum de ports TCP est requis pour accéder à votre réseau.
- Les applications qui utilisent des connexions hybrides accèdent uniquement à la ressource locale spécifique qui est publiée par l'intermédiaire de la connexion hybride.
- Elles peuvent se connecter à n'importe quelle ressource qui utilise un port TCP statique, par exemple, SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés.

	> [AZURE.NOTE]Actuellement, les services TCP qui utilisent des ports dynamiques (tels que le mode FTP passif ou le mode passif étendu) ne sont pas pris en charge.

- Elles peuvent être utilisées avec toutes les infrastructures prises en charge par Azure Web Apps (.NET, PHP, Java, Python, Node.js) et Azure Mobile Services (Node.js, .NET).
- Les applications Web Apps et Mobile Apps peuvent accéder aux ressources locales exactement de la même manière que si le site web ou le service mobile était situé sur votre réseau local. Par exemple, la chaîne de connexion utilisée localement peut également être utilisée sur Azure.


Les connexions hybrides peuvent également fournir aux administrateurs d'entreprise des possibilités de contrôle et de visibilité sur les ressources d'entreprise auxquelles elles accèdent, notamment les possibilités suivantes :

- À l'aide des paramètres de stratégie de groupe, les administrateurs peuvent autoriser les connexions hybrides sur le réseau et également désigner les ressources auxquelles elles peuvent accéder.
- Les journaux d'événements et d'audit sur le réseau d'entreprise fournissent la visibilité sur les ressources auxquelles accèdent les connexions hybrides.


## Configurations prises en charge

Les connexions hybrides prennent en charge les combinaisons d'infrastructure et d'application suivantes :

- Accès .NET Framework à SQL Server
- Accès .NET Framework aux services HTTP/HTTPS avec WebClient
- Accès PHP à SQL Server, MySQL
- Accès Java à SQL Server, MySQL et Oracle
- Accès Java aux services HTTP/HTTPS

Lorsque vous utilisez des connexions hybrides pour un accès local à SQL Server, tenez compte des éléments suivants :

- Les instances nommées SQL Express doivent être configurées pour utiliser des ports statiques. Par défaut, les instances nommées SQL Express utilisent des ports dynamiques.
- Les instances par défaut de SQL Express utilisent un port statique, mais TCP doit être activé. Par défaut, TCP n’est pas activé.
- À l’heure actuelle, le mode `MultiSubnetFailover=true` n’est pas pris en charge lorsque vous utilisez le clustering ou des groupes de disponibilité.
- Le `ApplicationIntent=ReadOnly` n’est actuellement pas pris en charge.
- L’authentification SQL peut être exigée en tant que méthode d’autorisation de bout en bout prise en charge par l’application Azure et le serveur SQL local.


## Ports et sécurité

Les connexions hybrides utilisent l’autorisation avec signature d’accès partagé pour sécuriser les connexions des applications Azure/du Gestionnaire de connexion hybride local à la connexion hybride. Des clés de connexion distinctes sont créées pour l’application et le Gestionnaire de connexion hybride local. Ces clés de connexion peuvent être substituées et révoquées indépendamment.

Les connexions hybrides assurent une distribution transparente et sécurisée des clés aux applications et au Gestionnaire de connexion hybride local.

Consultez la page [Création et gestion des connexions hybrides](integration-hybrid-connection-create-manage.md).

**L'autorisation de l'application est séparée de la connexion hybride**. Toute méthode d'autorisation appropriée peut être utilisée. Elle dépend des méthodes d'autorisation de bout en bout prises en charge dans le cloud Azure et sur les composants locaux. Par exemple, votre application Azure accède à un serveur SQL local. Dans ce scénario, l'autorisation SQL peut être la méthode d'autorisation prise en charge de bout en bout.

#### Ports TCP
Les connexions hybrides nécessitent seulement une connectivité TCP ou HTTP sortante à partir de votre réseau privé. Vous n’avez pas besoin d’ouvrir les ports du pare-feu ou de modifier la configuration du périmètre de votre réseau pour autoriser les connexions entrantes sur votre réseau.

Les ports TCP suivants sont utilisés par les connexions hybrides :

Port | Raison
--- | ---
9350 - 9354 | Ces ports sont utilisés pour la transmission de données. Le Gestionnaire de relais Service Bus sonde le port 9350 pour déterminer si la connectivité TCP est disponible. Si elle est disponible, il suppose que le port 9352 est également disponible. Le trafic de données passe par le port 9352. <br/><br/>Autoriser les connexions sortantes à ces ports.
5671 | Lorsque le port 9352 est utilisé pour le trafic de données, le port 5671 est utilisé en tant que canal de contrôle. <br/><br/>Autoriser les connexions sortantes à ce port. 
80, 443 | Si les ports 9352 et 5671 ne sont pas utilisables, *les ports 80 et 443* sont les ports de secours utilisés pour la transmission de données et le canal de contrôle.<br/><br/>Autoriser les connexions sortantes à ces ports. <br/><br/>\*\*Remarque\*\* Il est déconseillé d’utiliser ces ports de secours à la place des autres ports TCP. HTTP/WebSocket est utilisé comme protocole au lieu du protocole TCP natif pour les canaux de données. Cela peut entraîner une dégradation des performances.



## Suivant

[Création et gestion des connexions hybrides](integration-hybrid-connection-create-manage.md)<br/> [Accéder à des ressources locales à l’aide de connexions hybrides dans Azure App Service](../web-sites-hybrid-connection-get-started.md)<br/> [Connexion à un serveur SQL Server local à partir d’Azure Web Apps](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Azure Mobile Services et connexions hybrides](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)


## Voir aussi

[API REST pour la gestion des services BizTalk sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx) [Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md)<br/> [Créer un service BizTalk à l’aide du portail de gestion Azure](biztalk-provision-services.md)<br/> [Onglets Tableau de bord, Surveiller et Mettre à l’échelle dans BizTalk Services](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!---HONumber=August15_HO7-->