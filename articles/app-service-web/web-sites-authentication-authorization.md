<properties 
	pageTitle="Utiliser Active Directory pour l’authentification dans Azure App Service" 
	description="Découvrez les différentes options d’authentification et d’autorisation pour les applications métier déployées dans Azure App Service Web Apps" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="07/02/2015" 
	ms.author="cephalin"/>

# Utiliser Active Directory pour l’authentification dans Azure App Service #

[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) permet des scénarios d’application métier d’entreprise en prenant en charge l’authentification unique (SSO) des utilisateurs qui accèdent à l’application à partir de votre environnement local ou d’une connexion Internet publique. La solution peut être intégrée dans [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD) ou dans un service d'émission de jeton de sécurité (STS) local, comme les services ADFS (Active Directory Federation Services), pour authentifier les utilisateurs internes d'Active Directory (AD) et leur fournir une autorisation adéquate.

## Authentification et autorisation sans friction ##

En seulement quelques clics, vous pouvez activer l'authentification et l'autorisation pour votre application web. La configuration du style de case à cocher dans chaque application web Azure fournit un contrôle d'accès de base de votre application web métier. Pour cela, le protocole HTTPS et l'authentification sont appliqués au client Azure AD de votre choix avant que les utilisateurs ne soient autorisés à accéder au contenu de votre application web. Pour plus d’informations, consultez [Authentification et autorisation des applications Web](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/) (en anglais).

>[AZURE.NOTE]Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

## Implémentation manuelle de l’authentification et de l’autorisation ##

Dans de nombreux scénarios, vous souhaitez personnaliser le comportement d’authentification et d’autorisation de l’application (par exemple, avec une page de connexion et de déconnexion), personnaliser la logique d’autorisation, le comportement d’une application mutualisée, etc. Dans ce cas, il peut être plus intéressant de configurer l’authentification et l’autorisation manuellement pour une plus grande flexibilité de ses fonctionnalités. Les deux options principales sont expliquées ci-dessous.

-	[Azure AD](web-sites-dotnet-lob-application-azure-ad.md) : vous pouvez implémenter l’authentification et l’autorisation pour votre application Web avec Azure AD. L’utilisation d’Azure AD comme fournisseur d’identité présente les caractéristiques suivantes :
	-	Prise en charge de protocoles d’authentification populaires, comme [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) et [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Pour obtenir une liste complète des protocoles pris en charge, consultez [Protocoles d’authentification d’Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151124.aspx).
	-	Possibilité d'utiliser un fournisseur d'identité unique Azure sans aucune infrastructure locale.
	-	Possibilité de configurer également la synchronisation d'annuaires avec un AD local (géré localement).
	-	Avec Azure AD et la synchronisation d'annuaires à partir de votre domaine AD local, votre application web offre une expérience d'authentification unique (SSO) particulièrement fluide lorsque des utilisateurs AD y accèdent via l'intranet ou Internet. Depuis l’intranet, les utilisateurs AD peuvent accéder à l’application web automatiquement via une authentification intégrée. Depuis Internet,les utilisateurs AD peuvent se connecter à l’application web à l’aide de leurs informations d’identification Windows.
	-	Il fournit une authentification unique (SSO) à [toutes les applications prises en charge par Azure AD](/marketplace/active-directory/), y compris Azure, Office 365, Dynamics CRM Online, Microsoft InTune et des milliers d’applications de cloud autres que Microsoft. 
	-	Azure AD délègue la gestion des applications par [partie de confiance](http://en.wikipedia.org/wiki/Relying_party) à des rôles non-administrateurs, tandis que l’accès de l’application à des données d’annuaire sensibles doit encore être configuré par des administrateurs globaux.
	-	Envoi d’un ensemble de types de revendications à usage général pour toutes les applications par partie de confiance. Pour obtenir la liste des types de revendication, consultez [Types de jeton et de revendication pris en charge](http://msdn.microsoft.com/library/azure/dn195587.aspx). Les revendications ne peuvent pas être personnalisées.
	-	L’[API Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) permet à l’application d’accéder aux données d’annuaire dans Azure AD.
-	[Service d'émission de jeton de sécurité (STS) local, comme AD FS](../web-sites-dotnet-lob-application-adfs/) : vous pouvez implémenter l'authentification et l'autorisation pour votre application Web avec un STS local, comme AD FS. L'utilisation de services ADFS locaux présente les caractéristiques suivantes :
	-	La topologie des services ADFS doit être déployée localement, ce qui implique une surcharge de gestion et de coût.
	-	Cette solution est plus intéressante lorsque la stratégie d'entreprise implique le stockage local des données AD.
	-	Seuls les administrateurs AD FS peuvent configurer les [approbations et les règles de revendication des parties de confiance](http://technet.microsoft.com/library/dd807108.aspx).
	-	Il est possible de gérer les [revendications](http://technet.microsoft.com/library/ee913571.aspx) pour chaque application.
	-	Le service doit avoir une solution distincte pour accéder aux données AD locales via le pare-feu de l'entreprise.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=August15_HO6-->