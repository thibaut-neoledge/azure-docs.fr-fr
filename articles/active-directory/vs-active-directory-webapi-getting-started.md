<properties 
	pageTitle="Prise en main d’Azure Active Directory et des services connectés de Visual Studio (projets WebApi) |Microsoft Azure" 
	description="Comment prendre en main Azure Active Directory dans les projets WebApi après s’être connecté à un annuaire Azure AD ou avoir créé un annuaire Azure AD à l’aide des services connectés de Visual Studio" 
  services="active-directory"
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/18/2015" 
	ms.author="tarcher"/>

# Prendre en main Azure Active Directory et les services connectés de Visual Studio (projets WebApi)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Demander une authentification pour l'accès aux contrôleurs
 
Tous les contrôleurs de votre projet ont été dotés de l'attribut **Authorize**. Cet attribut permet de demander à l'utilisateur de s'authentifier avant d'accéder aux API définies par ces contrôleurs. Pour autoriser un accès anonyme au contrôleur, cet attribut doit être supprimé du contrôleur. Pour définir plus précisément les autorisations, appliquez cet attribut à chaque méthode nécessitant une autorisation, au lieu de l’appliquer à la classe de contrôleur.

[En savoir plus sur Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=AcomDC_1223_2015-->