<properties 
	pageTitle="Authentification et autorisation des applications métier sur Sites Web Azure" 
	description="Découvrez les différentes options d'authentification et d'autorisation pour les applications métier déployées sur Sites Web Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Authentification et autorisation des utilisateurs dans les applications métier sur Sites Web Azure #

[Sites Web Azure](http://azure.microsoft.com/fr-fr/services/websites/) permet des scénarios d'application métier d'entreprise en prenant en charge l'authentification unique (SSO) des utilisateurs, qu'ils accèdent à l'application à partir d'un environnement local ou d'une connexion Internet publique. Il peut être intégré à [Azure Active Directory](http://azure.microsoft.com/fr-fr/services/active-directory/) (AAD) ou à un service de jeton sécurisé (STS) local, tel que les services ADFS (Active Directory Federation Services), pour authentifier les utilisateurs internes d'Active Directory (AD) et leur fournir une autorisation adéquate.

## Authentification et autorisation sans friction ##

En seulement quelques clics, vous pouvez activer l'authentification et l'autorisation pour votre site Web. La configuration du style de case à cocher sur chaque site Web Azure fournit un contrôle d'accès de base de votre site Web métier. Cela est rendu possible par l'application du protocole HTTPS et de l'authentification au client Azure AD de votre choix avant de permettre aux utilisateurs d'accéder au contenu de tous les sites Web. Pour plus d'informations, consultez la page [Authentification et autorisation des sites Web Azure](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE] Cette fonctionnalité est actuellement à un stade préliminaire.

## Implémentation manuelle de l'authentification et de l'autorisation ##

Dans de nombreux scénarios, vous souhaitez personnaliser le comportement d'authentification et d'autorisation de l'application (tel qu'une page de connexion et de déconnexion), personnaliser la logique d'autorisation, le comportement d'une application mutualisée, etc. Dans ce cas, il peut être plus intéressant de configurer l'authentification et l'autorisation manuellement pour une plus grande flexibilité de ses fonctionnalités. Les deux options principales se trouvent ci-dessous.  

-	[Azure AD](../web-sites-dotnet-lob-application-azure-ad/) - Vous pouvez implémenter l'authentification et l'autorisation pour votre site Web avec Azure AD. L'utilisation d'Azure AD comme fournisseur d'identité présente les caractéristiques suivantes :
	-	La prise en charge des protocoles d'authentification populaires, tels que [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/), et [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Pour une liste complète des protocoles pris en charge, consultez la page [Protocoles d'authentification Azure Active Directory](http://msdn.microsoft.com/fr-fr/library/azure/dn151124.aspx).
	-	La possibilité d'utiliser un fournisseur d'identité unique Azure sans aucune infrastructure locale.
	-	La possibilité de configurer également la synchronisation d'annuaires avec un AD local (géré localement).
	-	Azure AD et la synchronisation d'annuaires à partir de votre domaine AD local vous offre une expérience d'authentification unique (SSO) en continu sur votre site Web lorsque des utilisateurs AD y accèdent via l'intranet ou Internet. Depuis l'intranet, les utilisateurs AD peuvent automatiquement accéder au site Web via une authentification intégrée. Depuis Internet,les utilisateurs AD peuvent se connecter au site Web à l'aide de leurs informations d'identification Windows.
	-	Il fournit une authentification unique (SSO) à [toutes les applications prises en charge par Azure AD](http://azure.microsoft.com/fr-fr/marketplace/active-directory/), y compris Azure, Office 365, Dynamics CRM Online, Windows InTune, et des milliers d'applications de cloud autres que Microsoft. 
	-	Azure AD délègue la gestion des applications [par partie de confiance](http://en.wikipedia.org/wiki/Relying_party) à des rôles non administrateurs, tandis que l'accès de l'application à des données d'annuaires sensibles doit encore être configuré par des administrateurs globaux.
	-	L'envoi d'un ensemble de types de revendications à usage général pour toutes les applications par partie de confiance. Pour la liste des types de revendications, consultez la page [Types de revendications et jetons pris en charge](http://msdn.microsoft.com/fr-fr/library/azure/dn195587.aspx). Les revendications ne peuvent pas être personnalisées.
	-	[L'API Azure AD Graph](http://msdn.microsoft.com/fr-fr/library/azure/hh974476.aspx) permet à l'application d'accéder aux données d'annuaires dans Azure AD.
-	[Service de jeton sécurisé(STS) local, tel que les services ADFS](../web-sites-dotnet-lob-application-adfs/) - Vous pouvez implémenter l'authentification et l'autorisation pour votre site Web avec un STS tel que les services ADFS. L'utilisation locale des services ADFS présente les caractéristiques suivantes :
	-	La topologie des services ADFS doit être déployée localement avec une surcharge de gestion et de coût.
	-	Cela est plus intéressant lorsque la politique d'entreprise demande le stockage local des données AD.
	-	Seuls les administrateurs ADFS peuvent configurer [les confiances et les règles de revendications de partie de confiance](http://technet.microsoft.com/fr-fr/library/dd807108.aspx).
	-	Il est possible de gérer les [revendications](http://technet.microsoft.com/fr-fr/library/ee913571.aspx) pour chaque application.
	-	Le service doit avoir une solution distincte pour accéder aux données AD locales via le pare-feu de l'entreprise.


<!--HONumber=42-->
