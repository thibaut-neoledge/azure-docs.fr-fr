<properties 
	pageTitle="Qu'est-ce qu'un connecteur et une application API BizTalk ?" 
	description="Découvrez les applications API, les connecteurs et les applications API BizTalk" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# Qu'est-ce qu'un connecteur et une application API BizTalk ?

Azure App Services repose sur un principe d'extensibilité et de connectivité commune via des applications API. Un *Connector* est un type d'application API axé sur la connectivité. Comme toute autre application API, les connecteurs s'utilisent à partir d'applications web, mobiles et logiques. Ils facilitent la connexion à des services existants et aident à gérer l'authentification, à fournir une analyse et bien plus encore.

N'importe quel développeur peut créer ses propres applications API et les déployer de manière privée, et sera prochainement en mesure de les partager et de les commercialiser via le Marketplace. 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

Pour accélérer la création de solutions avec Azure App Services, l'équipe Microsoft Azure a ajouté plusieurs connecteurs au Marketplace pour répondre à de nombreux scénarios courants. En outre, pour étendre la portée d'App Services aux scénarios d'intégration avancés et complexes, plusieurs fonctionnalités Premium et BizTalk sont également disponibles.

[Liste des connecteurs et applications API dans Microsoft Azure App Service](app-service-logic-connectors-list.md)


## Connecteurs de protocoles
App Services est né du web et privilégie la communication à l'aide du protocole HTTP et le partage des métadonnées par l'intermédiaire de formats ouverts comme Swagger. Bien entendu, les entreprises doivent pouvoir communiquer grâce à toute une gamme de protocoles. Les connecteurs de protocoles peuvent servir d'intermédiaire et rendre la communication avec App Services à l'aide des services FTP, SFTP, POP3/IMAP, SMTP et SOAP aussi simple qu'un appel HTTP.  

[Connecteurs de protocoles dans Microsoft Azure App Service](app-service-logic-protocol-connectors.md)


## Connecteurs SaaS
Les connecteurs SaaS App Services vous permettent de connecter vos applications web, mobiles et logiques aux plus grands noms du secteur SaaS, y compris Office 365, SalesForce, Sugar CRM, OneDrive, DropBox, Marketo, Facebook et bien plus encore.

[Connecteurs de réseaux sociaux dans Microsoft Azure App Service](app-service-logic-social-connectors.md)

[Connecteurs de services de données et d'applications dans Microsoft Azure App Service](app-service-logic-data-connectors.md)


## Connecteurs Premium 
Les connecteurs Premium étendent la portée d'App Services dans l'entreprise avec la connectivité pour SAP, Siebel, Oracle, DB2 et bien plus encore.

[Connecteurs d'entreprise dans Microsoft Azure App Service](app-service-logic-enterprise-connectors.md)


## Applications API BizTalk
La création d'applications professionnelles critiques nécessite plus qu'une simple connectivité. Basées sur la plateforme d'intégration Microsoft bien connue (BizTalk Server), les applications API BizTalk procurent des fonctionnalités d'intégration avancées qui peuvent être intégrées à vos applications web, mobiles et logiques en toute simplicité. Inclut le traitement par lots et le " debatching ", VETR (Valider, Extraire, Transformer et Router) et la prise en charge de formats EDI tels que X12, EDIFACT et AS2.

[Connecteurs et applications API B2B dans Microsoft Azure App Service](app-service-logic-b2b-connectors.md)<br/>

[Applications API d'intégration BizTalk dans Microsoft Azure App Service](app-service-logic-integration-connectors.md)

<!--HONumber=49-->