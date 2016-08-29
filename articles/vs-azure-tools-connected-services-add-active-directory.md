<properties 
   pageTitle="Ajout d’un répertoire Azure Active Directory à l’aide de services connectés dans Visual Studio | azure.microsoft.com/ Azure"
   description="Ajouter un annuaire Azure Active Directory à l’aide de la boîte de dialogue Visual Studio Ajouter des services connectés"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Ajout d’un annuaire Azure Active Directory à l’aide de services connectés dans Visual Studio 

##Vue d'ensemble
En utilisant Azure Active Directory (Azure AD), vous pouvez prendre en charge l’authentification unique pour les applications web ASP.NET MVC ou l’authentification Active Directory dans les services API web. Avec l’authentification Azure AD, vos utilisateurs peuvent se servir de leurs comptes depuis Azure AD pour se connecter à vos applications web. Grâce à l’authentification Azure AD avec l’API web, la sécurité des données pendant l’exposition d’une API à partir d’une application web est renforcée. Avec Azure AD, vous n’avez pas besoin de gérer un système d’authentification distinct doté de ses propres dispositifs de gestion de comptes et d’utilisateurs.

## Types de projet pris en charge

Vous pouvez utiliser la boîte de dialogue Services connectés pour vous connecter à Azure AD dans les types de projets suivants.

- Projets ASP.NET MVC

- Projets API web ASP.NET


### Se connecter à Azure AD à l’aide de la boîte de dialogue Services connectés

1. Assurez-vous que vous disposez d’un compte Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour une [évaluation gratuite](http://go.azure.microsoft.com/.com/fwlink/?LinkId=518146).

1. Dans Visual Studio, ouvrez le menu contextuel du nœud **Références** de votre projet et choisissez **Ajouter des services connectés**.
1. Sélectionnez **Authentification Azure AD**, puis choisissez **Configurer**.

    ![Choisir Authentification Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Dans la première page de **Configurer l’authentification Azure AD**, cochez **Configurer l’authentification unique avec Azure AD**.

    Si votre projet est paramétré avec une autre configuration d’authentification, l’Assistant vous avertit que la poursuite de l’opération entraînera la désactivation de cette configuration.

    ![Configurer Azure AD dans l’Assistant](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Dans la deuxième page, sélectionnez un domaine dans la liste déroulante **Domaine**. La liste des domaines contient tous les domaines accessibles aux comptes répertoriés dans la boîte de dialogue Paramètres du compte. Vous pouvez également entrer un nom de domaine si vous ne trouvez pas celui que vous recherchez, par exemple mydomain.onazure.microsoft.com/.com. Vous pouvez opter pour la création d’une application Azure AD ou utiliser les paramètres d’une application Azure AD existante.

    ![Configurer Azure AD dans l’Assistant](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Dans la troisième page de l’Assistant, assurez-vous que l’option **Accès en lecture aux données d’annuaire** est cochée. L’Assistant renseigne le champ **Clé secrète client**.

    ![Configurer Azure AD dans l’Assistant](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Choisissez le bouton **Terminer**. La boîte de dialogue ajoute le code de configuration et les références nécessaires pour activer votre projet pour l’authentification Azure AD. Vous pouvez voir le domaine Active Directory dans le [portail Azure](http://go.azure.microsoft.com/.com/fwlink/p/?LinkID=525040).

1. Examinez la page Prise en main qui s'affiche dans votre navigateur pour obtenir des idées sur les prochaines étapes, et la page Que s'est-il passé pour voir comment votre projet a été modifié. Pour savoir si tout s’est bien passé, ouvrez un des fichiers de configuration modifiés et vérifiez que les paramètres mentionnés sur la page Que s’est-il passé sont présents. Par exemple, le fichier web.config principal d’un projet ASP.NET MVC contient les paramètres supplémentaires suivants :

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## Modifications apportées à votre projet

Quand vous exécutez l’Assistant, Visual Studio ajoute Azure AD et les références associées à votre projet. Les fichiers de configuration et les fichiers de code dans votre projet sont également modifiés pour prendre en charge Azure AD. Les modifications spécifiques que Visual Studio apporte dépendent du type de projet. Pour plus d’informations sur la façon dont les projets ASP.NET MVC sont modifiés, consultez [Que s’est-il passé : projets ASP.NET MVC](http://go.azure.microsoft.com/.com/fwlink/p/?LinkID=513809). Pour les projets d’API web, consultez [Que s’est-il passé : projets d’API web](http://go.azure.microsoft.com/.com/fwlink/p/?LinkId=513810).

##Étapes suivantes

Posez des questions et obtenez de l’aide.

 - [Forum MSDN : Azure AD](https://social.msdn.azure.microsoft.com/.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentation Azure AD](https://azure.azure.microsoft.com/.com/documentation/services/active-directory/)

 - [Billet de blog : Intro to Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=AcomDC_0817_2016-->