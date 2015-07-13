<properties 
	pageTitle="Fonctionnement d’Azure AD Connect" 
	description="Découvrez comment fonctionne Azure AD Connect" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Fonctionnement d’Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Présentation">Présentation</a> <a href="../active-directory-aadconnect-how-it-works/" title="Fonctionnement" class="current">Fonctionnement</a> <a href="../active-directory-aadconnect-get-started/" title="Prise en main">Prise en main</a> <a href="../active-directory-aadconnect-whats-next/" title="Étapes suivantes">Étapes suivantes</a> <a href="../active-directory-aadconnect-learn-more/" title="En savoir plus">En savoir plus</a>
</div>
Azure Active Directory Connect est constitué de trois parties principales. Il s’agit des services de synchronisation, de la partie facultative Active Directory Federation Services. La partie surveillance est effectuée à l’aide d’[Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Synchronisation : cette partie est constituée des composants et des fonctionnalités précédemment publiés sous le nom de synchronisation d’annuaires et AAD Sync. Il s’agit de la partie chargée de créer les utilisateurs et les groupes. Elle est également chargée de s’assurer que les informations relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud.
- AD FS : ceci est une partie facultative d’Azure AD Connect, qui peut être utilisée pour configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Cette partie permet aux organisations de faire face à des déploiements complexes, par exemple l’authentification unique de jonction de domaine, l’application de la stratégie de connexion AD ou l’utilisation de cartes à puce ou d’une solution tierce d’authentification multifacteur. Pour plus d’informations sur la configuration de l’authentification unique, consultez [DirSync avec authentification unique](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Analyse d’intégrité : pour les déploiements complexes avec AD FS, Azure AD Connect peut assurer la surveillance de vos serveurs de fédération et offrir l’accès à un emplacement central dans le portail Azure pour afficher ces activités. Pour plus d’informations, consultez [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


## Composants de prise en charge d’Azure AD Connect

Voici une liste de prérequis et de composants de prise en charge qu’Azure AD Connect installe sur le serveur sur lequel vous configurez Azure AD Connect. Cette liste est destinée à une installation rapide de base. Si vous choisissez d’utiliser un autre serveur SQL Server sur la page d’installation des services de synchronisation, les composants SQL Server 2012 ci-dessous ne sont pas installés.

- Connecteur Azure AD d’Azure AD Connect
- Utilitaires de ligne de commande Microsoft SQL Server 2012
- Client natif Microsoft SQL Server 2012
- Base de données locale Microsoft SQL Server 2012 Express
- Module Azure Active Directory pour Windows PowerShell
- Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique
- Package de redistribution Microsoft Visual C++ 2013




 

<!---HONumber=62-->