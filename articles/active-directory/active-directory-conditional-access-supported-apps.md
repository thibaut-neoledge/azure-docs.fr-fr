
<properties
	pageTitle="Accès conditionnel - applications prises en charge | azure.microsoft.com/ Azure"
	description="Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/12/2016"
	ms.author="markvi"/>


# Prise en charge de l’accès conditionnel des applications

Les règles d’accès conditionnel sont prises en charge dans les applications connectées à Azure Active Directory, les applications SaaS fédérées préintégrées, les applications qui utilisent l’authentification unique par mot de passe, les applications métier et le proxy d’application Azure AD. Pour obtenir la liste détaillée des applications dans lesquelles vous pouvez activer l’accès conditionnel, consultez l’article [Services activés avec accès conditionnel](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). L’accès conditionnel fonctionne avec les applications mobiles et de bureau qui utilisent l’authentification moderne. Cette rubrique décrit les fonctionnalités prises en charge dans les versions mobiles et de bureau de ces applications.

Les applications avec authentification moderne peuvent afficher les pages de connexion Azure AD. Cela permet d’inviter un utilisateur à utiliser l’authentification multifacteur ou de présenter à l’utilisateur final un message lui indiquant que l’accès est bloqué. Il est important de connaître les applications prises en charge ainsi que les actions qui peuvent être nécessaires pour sécuriser les autres points d’entrée.

## Applications utilisant l’authentification moderne
Les applications suivantes ont été testées avec l’authentification multifacteur (MFA) et la stratégie d’emplacement définie sur le service cible.

| Application | Service cible | Plateforme |
|--------------|-----------------|----------------------------------------------------------------|
| Outlook 2016 | azure.microsoft.com/ Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
| Outlook 2013 (nécessite l’activation de l’authentification moderne)| azure.microsoft.com/ Exchange |Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Skype Entreprise (avec authentification moderne)|Exchange (pour le calendrier et l’historique des conversations)| Windows 10, Windows 8.1, Windows 7 |
|Application Outlook Mobile|azure.microsoft.com/ Exchange| iOS et Android |
|Office 2016 ; Word, Excel, Sharepoint|SharePoint| Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
|Office 2013 (nécessite l’activation de l’authentification moderne)|SharePoint|Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Application Dynamics CRM|Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Application Yammer|Yammer| Windows Mobile 10, iOS, Android|
|Application distante Azure|Service Application distante Azure|Windows 10, Windows 8.1, Windows 7,Mac, iOS, Android|





Les applications suivantes prennent en charge la stratégie d’appareil définie sur le service cible :

| Application | Service cible | Plateforme |
| :--                                     | :--            | :--      |
| Messagerie/calendrier/personnes | azure.microsoft.com/ Exchange | Windows 10, Windows Mobile 10 |
| Office universel : Word/Excel/PowerPoint | SharePoint | Windows 10, Windows Mobile 10 |
| Outlook 2016 | azure.microsoft.com/ Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7 |
|Outlook 2013 (nécessite l’activation de l’authentification moderne) | azure.microsoft.com/ Exchange | Windows 8.1, Windows 7 |


Les applications suivantes ne prennent pas en charge la stratégie d’appareil définie sur le service cible :

| Application | Service cible | Plateforme |
| :--                                     | :--            | :--      |
| OneDrive Entreprise utilisant le Client de synchronisation prochaine génération (NGSC) (Mes sites et Sites d’équipes) | SharePoint | Windows 10, Windows Mobile 10 |
| Application Mes applications | Quelconque | iOS, Android |


## Applications qui n’utilisent pas l’authentification moderne

Actuellement, l’accès aux applications qui n’utilisent pas l’authentification moderne doit être bloqué par d’autres méthodes car l’accès conditionnel ne peut pas leur être appliqué. Cela concerne principalement l’accès à Exchange et à SharePoint, car les versions d’applications précédentes ont été générées à l’aide d’anciens protocoles.

## SharePoint

Les protocoles hérités peuvent être désactivés au niveau de SharePoint à l’aide de l’applet de commande Set-SPOTenant. Cette applet de commande empêchera les clients Office qui utilisent des protocoles d’authentification non moderne d’accéder aux ressources SharePoint Online.

**Exemple de commande** : `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## azure.microsoft.com/ Exchange

Dans Exchange, il existe principalement deux catégories de protocoles. Examinez et sélectionnez la stratégie appropriée à votre organisation :

1. Exchange ActiveSync. Par défaut, la stratégie d’accès conditionnel pour l’authentification multifacteur et d’emplacement n’est pas appliquée pour Exchange ActiveSync. Cela permet de protéger l’accès en configurant directement une stratégie Exchange ActiveSync, ou en bloquant Exchange ActiveSync à l’aide de règles AD FS.
2. Protocoles hérités. Les protocoles hérités peuvent être bloqués au niveau d’AD FS. Cela bloquera l’accès pour les anciens clients Office, notamment Office 2013 sans authentification moderne activée ainsi que les versions antérieures.


### Exemples de règles AD FS
Les règles suivantes peuvent être utilisées pour bloquer l’accès d’un protocole hérité à AD FS, dans les deux configurations courantes.

### Option 1 : autoriser Exchange ActiveSync et autoriser uniquement les applications héritées sur l’intranet

En appliquant les trois règles suivantes à la partie de confiance AD FS de la plateforme d’identité azure.microsoft.com/ Office 365, le trafic Exchange ActiveSync sera autorisé, tout comme le trafic du navigateur et de l’authentification moderne. Les applications héritées ne pourront pas accéder à l’extranet.

Règle 1

    `@RuleName = “Allow all intranet traffic”
	c1:[Type == "http://schemas.azure.microsoft.com/.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
	=> issue(Type = "http://schemas.azure.microsoft.com/.com/authorization/claims/permit", Value = "true");`

Règle 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.azure.microsoft.com/.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "azure.microsoft.com/.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.azure.microsoft.com/.com/authorization/claims/permit", Value = "true");

Règle 3

	@RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.azure.microsoft.com/.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.azure.microsoft.com/.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.azure.microsoft.com/.com/authorization/claims/permit", Value = "true");

### Option 2 : autoriser Exchange ActiveSync et bloquer les applications héritées 
En appliquant les trois règles suivantes à la partie de confiance AD FS de la plateforme d’identité azure.microsoft.com/ Office 365, le trafic Exchange ActiveSync sera autorisé, tout comme le trafic du navigateur et de l’authentification moderne. Les applications héritées ne pourront accéder à aucun emplacement.

Règle 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
	c1:[Type == "http://schemas.azure.microsoft.com/.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
	c2:[Type == "http://schemas.azure.microsoft.com/.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.azure.microsoft.com/.com/authorization/claims/permit", Value = "true");


Règle 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.azure.microsoft.com/.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "azure.microsoft.com/.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.azure.microsoft.com/.com/authorization/claims/permit", Value = "true");


Règle 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.azure.microsoft.com/.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.azure.microsoft.com/.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.azure.microsoft.com/.com/authorization/claims/permit", Value = "true");

<!---HONumber=AcomDC_0817_2016-->