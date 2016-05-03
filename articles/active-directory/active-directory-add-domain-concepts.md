<properties
	pageTitle="Vue d’ensemble conceptuelle des noms de domaine personnalisés dans Azure Active Directory | Microsoft Azure"
	description="Explique l’infrastructure conceptuelle pour l’utilisation des noms de domaine personnalisés dans Azure Active Directory, notamment la fédération pour l’authentification unique"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Vue d’ensemble conceptuelle des noms de domaine personnalisés dans Azure Active Directory

Un nom de domaine est une partie importante de l’identificateur de nombreuses ressources de répertoire : il fait partie du nom ou de l’adresse électronique d’un utilisateur, de l’adresse d’un groupe et parfois de l’URI ID d’application pour une application. Une ressource dans Azure Active Directory (Azure AD) peut inclure un nom de domaine déjà vérifié comme appartenant au répertoire qui contient la ressource. Seul un administrateur général peut effectuer des tâches de gestion de domaine dans Azure AD.

Les noms de domaine dans Azure AD sont globalement uniques. Un nom de domaine ne peut être utilisé que par un seul service Azure AD. Si un répertoire Azure AD a vérifié un nom de domaine, aucun autre répertoire Azure AD ne peut vérifier ou utiliser ce même nom de domaine.

## Noms de domaine initial et personnalisé

Chaque nom de domaine dans Azure AD est un nom de domaine initial ou un nom de domaine personnalisé.

Chaque service Azure AD est fourni avec un nom de domaine initial dans le formulaire contoso.onmicrosoft.com. Le troisième niveau du nom de domaine, « contoso.com » dans cet exemple, a été établi lorsque le répertoire a été créé, généralement par l’administrateur qui a créé le répertoire. Le nom de domaine initial d’un répertoire ne peut pas être modifié ou supprimé. Le nom de domaine initial, bien qu’entièrement fonctionnel, est principalement destiné à être utilisé comme un mécanisme d’amorçage.

Dans la plupart des environnements de production, un répertoire contient au moins un domaine personnalisé vérifié, par exemple « contoso.com », et c’est ce domaine personnalisé qui est visible aux utilisateurs finaux. Un nom de domaine personnalisé est une ressource ajoutée par l’administrateur général d’un répertoire. Un nom de domaine personnalisé est un domaine qui appartient à cette organisation, par exemple, « contoso.com ». La plupart des organisations possèdent déjà un nom de domaine pour l’hébergement de leurs sites web. Ce nom de domaine est connu des employés, car il fait partie du nom d’utilisateur dont ils se servent pour se connecter au réseau d’entreprise, ou pour envoyer et récupérer le courrier électronique. Avant de pouvoir être utilisé par un répertoire, le nom de domaine personnalisé doit être vérifié.

## Noms de domaine vérifiés et non vérifiés

Le nom de domaine initial d’un répertoire est implicitement évalué comme vérifié par Azure AD. Lorsqu’un administrateur ajoute un nom de domaine personnalisé à Azure AD, celui-ci est initialement dans un état non vérifié. Azure AD n’autorise aucune ressource du répertoire à utiliser un domaine non vérifié. Cela garantit qu’un seul répertoire peut utiliser un nom de domaine personnalisé et que l’organisation qui possède le répertoire Azure AD est aussi celle qui possède le nom de domaine.

Azure AD vérifie la propriété d’un nom de domaine en recherchant des entrées particulières dans les enregistrements DNS pour le domaine. Pour vérifier la propriété d’un domaine, un administrateur obtient les entrées DNS qu’Azure AD recherchera et ajoute ces entrées aux enregistrements DNS pour le nom de domaine maintenu par le bureau d’enregistrement du nom de domaine pour ce domaine. Les étapes permettant de vérifier un domaine sont décrites dans l’article [Ajout d’un domaine personnalisé à votre répertoire Azure AD](active-directory-add-domain.md).

L’ajout d’entrées DNS à Azure AD pour vérifier la propriété du domaine n’affecte pas les autres services de domaine, tels que le courrier électronique ou l’hébergement web.

## Noms de domaines fédérés et gérés

Un nom de domaine personnalisé dans Azure AD peut être configuré pour permettre une authentification fédérée entre Active Directory local et Azure AD. La configuration d’un domaine pour la fédération requiert des mises à jour vers des ressources privilégiées dans Azure AD et dans Active Directory local. La configuration d’un domaine fédéré doit être effectuée à partir d’Azure AD Connect ou à l’aide de PowerShell. La fédération d’un domaine personnalisé ne peut pas être lancée à partir du portail Azure Classic. [Regardez cette vidéo pour en savoir plus sur la configuration AD FS pour la connexion utilisateur avec Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Les domaines qui ne sont pas fédérés sont parfois appelés domaines gérés. Le nom de domaine initial d’un répertoire Azure AD est implicitement évalué comme domaine géré.

## Noms de domaines principaux

Le nom de domaine principal d’un répertoire est le nom de domaine présélectionné en tant que valeur par défaut pour la partie « domaine » du nom d’utilisateur, lorsqu’un administrateur crée un utilisateur dans le [portail Azure Classic](https://manage.windowsazure.com/) ou un dans un autre portail, tel que le portail d’administration d’Office 365. Un répertoire Azure AD ne peut avoir qu’un seul nom de domaine principal. Un administrateur peut modifier le nom de domaine principal en n’importe quel domaine personnalisé vérifié non fédéré ou en nom de domaine initial.

## Noms de domaine dans Azure AD et dans les autres services Microsoft Online Services

Un nom de domaine doit être vérifié dans Azure AD avant de pouvoir être utilisé par d’autres services Microsoft Online Services, tels qu’Exchange Online, SharePoint Online et Intune. Ces autres services requièrent généralement qu’un administrateur ajoute au moins une entrée DNS spécifique au service.

Une application web Azure utilise son propre mécanisme pour vérifier la propriété d’un domaine. Un domaine doit être vérifié pour une utilisation avec Azure AD, même s’il a été précédemment vérifié pour une utilisation par une application web Azure dans un abonnement qui s’appuie sur Azure AD. Une application web Azure peut utiliser un nom de domaine qui a été vérifié dans un autre répertoire Azure AD que le répertoire qui sécurise l’application web.

## Gestion des noms de domaine

Les tâches de gestion de domaine peuvent être effectuées à partir du portail Azure Classic et de PowerShell. De nombreuses de tâches peuvent être effectuées à l’aide d’API Graph Azure AD (en version préliminaire publique).

-   [Ajout et vérification d’un nom de domaine personnalisé](active-directory-add-domain.md)

-   [Gestion des domaines dans le portail Azure Classic](active-directory-add-manage-domain-names.md)

-   [Utilisation de PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Utilisation d’API Graph Azure AD pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

<!---HONumber=AcomDC_0420_2016-->