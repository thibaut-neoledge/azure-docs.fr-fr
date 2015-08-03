<properties 
	pageTitle="Applications multicanaux d'entreprise" 
	description="Vue d'ensemble de la façon dont une application multicanal couvre les ressources locales et les services logiciels cloud." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# Créer des applications multicanaux pour l'entreprise

## Vue d'ensemble

Les applications multicanaux d'entreprise présentent les données aux clients grâce à plusieurs technologies. Les consommateurs web, mobiles et d'API d'entreprise récupèrent et traitent des informations issues de diverses sources. Ces sources s’étendent aux systèmes internes exécutés localement et aux services basés dans le cloud.

Les applications d'entreprise ont également besoin d'un accès sécurisé, avec les employés et partenaires commerciaux qui se connectent aux données à l'aide d'identités sécurisées sous le contrôle direct de l'entreprise.

Les applications d'entreprise en cours d'exécution sur Azure App Service (AAS) peuvent fournir toutes ces fonctionnalités.

L’exemple d’application de déplacement des employés ci-dessous montre une application multicanal d’entreprise sécurisée via Azure Active Directory (AAD) qui s’intègre à la fois aux ressources locales et aux services SaaS (Software-as-a-Service) comme Office 365 et Salesforce.

## <a name="acceptablefiles"></a>Contrôle d’accès avec Azure Active Directory

Les utilisateurs de l'application de déplacement doivent d'abord s'authentifier auprès d'un annuaire Active Directory d'entreprise. Quelques étapes ont été suivies pour configurer l'application afin d'utiliser Azure Active Directory (AAD) :

* Un annuaire Azure Active Directory a été créé pour l'entreprise.
* L'annuaire Active Directory local de l'entreprise a été fédéré avec Azure Active Directory.
* Enfin, l'application a été inscrite auprès d'AAD à l'aide de la simple fonctionnalité d'intégration AAD d'Azure App Service. 

Le résultat final est que l'application invite l'utilisateur à se connecter à AAD (et par extension, à l'annuaire AD de l'entreprise).
	
![Connexion AAD][AADLogin]

Pour plus d’informations sur la configuration de l’intégration d’AAS à AAD, consultez [Intégration AAS à AAD][AASIntegrationwithAAD].

## <a name="acceptablefiles"></a>Accès aux ressources locales

Une fois qu'un utilisateur est connecté à AAD, la liste des voyages d'entreprise prévus s'affiche. Étant donné que l'application web s'exécute dans Microsoft Azure, il a besoin d'un moyen d'accéder au serveur SQL Server local qui contient ces informations.

![Données sur serveur SQL Server local][DatafromOnpremisesSqlServer]

L'application est configurée avec la fonctionnalité d'intégration de réseau virtuel de point à site. Celle-ci permet à l'application d'utiliser la logique d'accès aux données standard (dans ce cas, Entity Framework) pour accéder en toute transparence à un serveur SQL Server distant qui s'exécute dans le réseau de l'entreprise.

Pour plus d’informations sur l’intégration de réseau virtuel de point à site, consultez la page [Intégration de réseau virtuel][VNETIntegration].

## <a name="acceptablefiles"></a>Intégration à Office 365

Chaque fois qu'un employé crée un enregistrement de déplacement, l'application web crée une demande de déplacement dans une liste SharePoint Online. Il existe un lien dans l'application que les employés peuvent utiliser pour accéder facilement à la liste Sharepoint :

![Lien de la liste SharePoint][SharepointListLink]

le fait de cliquer sur le lien permet d'accéder automatiquement à la liste SharePoint. Étant donné que les employés sont connectés à leur AAD d'entreprise, ils sont authentifiés de manière transparente auprès d'Office 365 à l'aide du jeton de sécurité émis par AAD.

![Liste SharePoint][SharepointList]

L'application web crée également un document de déplacement dans une bibliothèque de documents Sharepoint Online.

![Bibliothèque de documents SharePoint][SharepointDocumentLibrary]

Les biens créés dans SharePoint Online permettent à l'application web d'exploiter les fonctionnalités dans Office 365. Par exemple, un workflow de décision/d'approbation peut être déclenché chaque fois qu'un élément est créé dans la liste Sharepoint.

Pour plus d’informations sur l’intégration d’Office 365, consultez [Intégration d’Office 365][Office365Integration]

## <a name="acceptablefiles"></a>Intégration aux services SaaS

Les entreprises aujourd'hui utilisent une variété de services SaaS et ont besoin d'interagir avec les données SaaS à partir d'autres applications. L’application de voyage est un exemple de ce scénario. Elle met à jour les informations de compte client dans Salesforce chaque fois qu'un employé envisage un déplacement chez un compte client.

![Intégration Salesforce][SalesforceIntegration]

Le compte Salesforce de l'entreprise est configuré avec AAD pour permettre une authentification transparente auprès de Salesforce à l'aide des informations d'identification AAD. Par conséquent, une fois que les employés sont connectés à l'application de déplacement à l'aide d'AAD, l'application peut lire et écrire des données stockées dans Salesforce.

Pour plus d’informations sur l’intégration de SaaS, consultez la page [Intégration de SaaS][SaaSIntegration]

## <a name="NextSteps"></a>Étapes suivantes

Pour plus d’informations, consultez la page [Azure Application Services][AzureApplicationServices]
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-office365/
[SaaSIntegration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-salesforce/
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!---HONumber=July15_HO4-->