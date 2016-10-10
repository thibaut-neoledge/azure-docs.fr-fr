<properties
    pageTitle="Référence d’API de création de rapports sur l’activité de connexion Azure Active Directory | Microsoft Azure"
    description="Référence d’API de création de rapports sur l’activité de connexion Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Référence d’API de création de rapports sur l’activité de connexion Azure Active Directory


Cette rubrique fait partie d’un ensemble de rubriques relatives à l’API de création de rapports Azure Active Directory. La création de rapports Azure AD fournit une API qui vous permet d’accéder aux données des rapports sur l’activité de connexion à l’aide de code ou d’outils associés. Cette rubrique a pour but de vous fournir des informations de référence sur **l’API de création de rapports sur l’activité de connexion**.

Consultez l'article :

- [Activités de connexion](active-directory-reporting-azure-portal.md#sign-in-activities) pour plus d’informations conceptuelles
- [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md) pour plus d’informations sur l’API de création de rapports.

Si vous avez des questions, des problèmes ou des commentaires, veuillez contacter [Aide à la création de rapports AAD](mailto:aadreportinghelp@microsoft.com).



## Qui peut accéder aux données de l’API ?

- Utilisateurs ayant le rôle d’administrateur de sécurité ou de lecteur de la sécurité

- Administrateurs généraux

- Toute application qui a l’autorisation d’accéder à l’API (l’autorisation de l’application peut être configurée uniquement en fonction de l’autorisation Administrateur général)



## Composants requis

Pour accéder à ce rapport via l’API de création de rapports, vous devez disposer des éléments suivants :

- Une [édition Azure Active Directory Premium P1 ou P2](active-directory-editions.md)

- Avoir respecté la [configuration requise pour accéder à l’API de création de rapports Azure AD](active-directory-reporting-api-prerequisites.md).


##Accès à l’API

Vous pouvez soit accéder à cette API via [l’Afficheur Graph](https://graphexplorer2.cloudapp.net), soit par programme à l’aide, par exemple, de PowerShell. Pour que PowerShell puisse interpréter correctement la syntaxe de filtre OData utilisée dans les appels REST Graph AAD, vous devez utiliser le caractère accent grave (`) pour « échapper » au caractère $. Le caractère accent grave sert de [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), ce qui permet à PowerShell d’effectuer une interprétation littérale du caractère $ et de ne pas le confondre avec un nom de variable PowerShell (par exemple : $filter).

Cette rubrique porte sur l’Afficheur Graph. Pour obtenir un exemple PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## Point de terminaison d’API

Vous pouvez accéder à cette API à l’aide de l’URI de base suivant :
	
	https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



En raison du volume de données, cette API est limitée à un million d’enregistrements retournés.

Cet appel renvoie les données par lots. Chaque lot comporte un maximum de 1 000 enregistrements. Pour obtenir le lot d’enregistrements suivant, cliquez sur le lien Suivant. Obtenez les informations du [jeton d’évitement](https://msdn.microsoft.com/library/dd942121.aspx) dans le premier jeu d’enregistrements retournés. Le jeton d’évitement se trouve à la fin du jeu de résultats.

	https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## Filtres pris en charge

Vous pouvez réduire le nombre d’enregistrements qui sont retournés par un appel d’API à l’aide d’un filtre. Pour les données liées à l’API de connexion, les filtres suivants sont pris en charge :

- **$top=<nombre d’enregistrements à retourner>** : pour limiter le nombre d’enregistrements retournés. Il s’agit d’une opération coûteuse. N’utilisez pas ce filtre si vous souhaitez retourner des milliers d’objets.
- **$filter=<votre instruction de filtre>** : pour spécifier, en fonction des champs de filtre pris en charge, les types d’enregistrements qui vous intéressent



## Opérateurs et champs de filtre pris en charge

Pour indiquer le type d’enregistrements qui vous intéressent, vous pouvez créer une déclaration de filtre contenant l’un des champs de filtre suivants ou une combinaison de ceux-ci :

- [signinDateTime](#signindatetime) : définit une date ou une plage de dates

- [userId](#userid) : définit un utilisateur spécifique en fonction de l’ID de l’utilisateur.

- [userPrincipalName](#userprincipalname) : définit un utilisateur spécifique en fonction du nom d’utilisateur principal (UPN) de l’utilisateur

- [appId](#appid) : définit une application spécifique en fonction de l’ID de l’application

- [appDisplayName](#appdisplayname) : définit une application spécifique en fonction du nom d’affichage de l’application

- [loginStatus](#loginStatus) : définit l’état des connexions (réussite / échec)


> [AZURE.NOTE] Lorsque vous utilisez l’Afficheur Graph, vous devez respecter la casse pour chaque lettre dans les champs de filtre.


Pour réduire l’étendue des données retournées, vous pouvez créer une combinaison des filtres et des champs de filtres pris en charge. Par exemple, l’instruction suivante retourne les 10 premiers enregistrements entre le 1er juillet 2016 et le 6 juillet 2016 :

	https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### signinDateTime

**Opérateurs pris en charge** : eq, ge, le, gt, lt

**Exemple** :

Utilisation d’une date spécifique

	$filter=signinDateTime+eq+2016-04-25T23:59:00Z	



Utilisation d’une plage de dates

	$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Remarques** :

Le paramètre datetime doit être au format UTC


----------

### userId

**Opérateurs pris en charge** : eq

**Exemple** :

	$filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Remarques** :

La valeur d’userId est une valeur de chaîne



----------

### userPrincipalName

**Opérateurs pris en charge** : eq

**Exemple** :

	$filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Remarques** :

La valeur d’userPrincipalName est une valeur de chaîne

----------

### appId

**Opérateurs pris en charge** : eq

**Exemple** :

	$filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Remarques** :

La valeur d’appId est une valeur de chaîne

----------


### appDisplayName

**Opérateurs pris en charge** : eq

**Exemple** :

	$filter=appDisplayName+eq+'Azure+Portal' 


**Remarques** :

La valeur d’appDisplayName est une valeur de chaîne

----------

### loginStatus

**Opérateurs pris en charge** : eq

**Exemple** :

	$filter=loginStatus+eq+'1'  


**Remarques** :

Il existe deux options pour loginStatus : 0 - Réussite, 1 - Échec

----------



## Étapes suivantes

- Voulez-vous voir des exemples d’activités de connexion filtrées ? Consultez les [exemples de l’API de création de rapports sur l’activité de connexion Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).

- Vous souhaitez en savoir plus sur l’API de création de rapports Azure AD ? Consultez [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md).

<!---HONumber=AcomDC_0928_2016-->