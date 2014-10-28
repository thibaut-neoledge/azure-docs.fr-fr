<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Developing Single Tenant Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan"></tags>

# Développement d'une application cloud dédiée avec Azure Active Directory

## <a name="introduction"></a>Introduction

Ce didacticiel montre comment tirer parti d'Azure Active Directory pour authentifier les utilisateurs d'un annuaire et lire leurs données à partir de ce dernier. Vous apprendrez à :

-   configurer l'application web dans le client d'une société ;
-   protéger l'application avec WS-Federation.
-   accéder à l'annuaire avec l'API Graph.

### Conditions préalables

Les éléments de développement suivants sont requis pour cette procédure pas à pas :

-   Visual Studio 2010 SP1
-   Microsoft .NET Framework 4.0
-   [ASP.NET MVC 3][ASP.NET MVC 3]
-   [Windows Identity Foundation 1.0 Runtime][Windows Identity Foundation 1.0 Runtime]
-   [Windows Identity Foundation 3.5 SDK][Windows Identity Foundation 3.5 SDK]
-   [WCF Data Services pour OData V3][WCF Data Services pour OData V3]
-   IIS (Internet Information Services) 7.5 avec SSL activé
-   Windows PowerShell
-   [Cmdlets PowerShell pour Office 365][Cmdlets PowerShell pour Office 365]

### Sommaire

-   [Introduction][Introduction]
-   [Étape 1 : création d'une application ASP.NET MVC][Étape 1 : création d'une application ASP.NET MVC]
-   [Étape 2 : configuration de l'application dans le client d'annuaire d'une société][Étape 2 : configuration de l'application dans le client d'annuaire d'une société]
-   [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés][Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]
-   [Étape 4 : lecture des données de l'annuaire avec l'API Graph][Étape 4 : lecture des données de l'annuaire avec l'API Graph]
-   [Résumé][Résumé]

## <a name="createapp"></a>Étape 1 : Création d'une application ASP.NET MVC

Cette étape montre comment créer une application ASP.NET MVC simple qui représentera une source protégée. L'accès à cette ressource sera accordé via une authentification fédérée gérée par le service d'émission de jeton de sécurité (STS) d'une société, qui est décrit ultérieurement dans ce didacticiel.

1.  Ouvrez Visual Studio en tant qu'administrateur.
2.  Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
3.  Dans le menu du modèle, à gauche, sélectionnez **Web**, puis cliquez sur **Application web ASP.NET MVC 3**. Nommez le projet *OrgIdFederationSample*.
4.  Dans la boîte de dialogue **Nouveau projet ASP.NET MVC 3**, sélectionnez le modèle **Vide**, puis cliquez sur **OK**.
5.  Visual Studio génère le nouveau projet. Cliquez ensuite avec le bouton droit sur le dossier **Contrôleurs** dans l'**Explorateur de solutions**, cliquez sur **Ajouter**, puis sur **Contrôleur**.
6.  Dans la boîte de dialogue **Ajouter un contrôleur**, entrez le nom *HomeController.cs* pour le nouveau contrôleur, puis cliquez sur **Ajouter**.
7.  Le fichier **HomeController.cs** est créé et s'ouvre. Dans le fichier de code, cliquez avec le bouton droit sur la méthode ***Index()***, puis cliquez sur **Ajouter une vue...**.
8.  Dans la boîte de dialogue **Ajouter une vue**, cliquez sur **OK**. Un fichier **Index.cshtml** est créé dans un nouveau dossier nommé **Home**.
9.  Cliquez avec le bouton droit sur le projet **OrgIdFederationSample** dans l'**Explorateur de solutions**, puis cliquez sur **Propriétés**.
10. Dans la fenêtre de propriétés, cliquez sur **Web** dans le menu de gauche, puis sélectionnez **Utiliser le serveur web IIS local**. Selon les cas, une boîte de dialogue vous demandant de créer un répertoire virtuel pour le projet peut s'afficher. Cliquez sur **Oui**.
11. Générez et exécutez l'application. La page d'index de votre contrôleur d'accueil s'affiche.

## <a name="provisionapp"></a>Étape 2 : configuration de l'application dans le client d'annuaire d'une société

Cette étape explique comment l'administrateur d'une société utilisant Azure Active Directory met en service l'application MVC dans son client et configure l'authentification unique. Une fois cette étape terminée, les employés de la société peuvent s'authentifier pour accéder à l'application web en utilisant leurs comptes Office 365.

Le processus de configuration commence par la création d'un principal du service pour l'application. Azure Active Directory utilise les principaux du service pour enregistrer et authentifier des applications dans l'annuaire.

1.  Si ce n'est pas déjà fait, téléchargez et installez les cmdlets PowerShell pour Office 365.
2.  Dans le menu **Démarrer**, exécutez la console **Module Microsoft Online Services pour Windows PowerShell**. Celle-ci fournit un environnement en ligne de commande permettant de configurer des attributs de votre client Office 365, comme la création et la modification des principaux du service.
3.  Pour importer le module **MSOnlineExtended** requis, entrez la commande suivante, puis appuyez sur Entrée :

        Import-Module MSOnlineExtended -Force

4.  Pour vous connecter à votre annuaire Office 365, vous devez fournir les informations d'identification de l'administrateur de la société. Tapez la commande suivante et appuyez sur Entrée, puis entrez vos informations d'identification lorsque vous y êtes invité :

        Connect-MsolService

5.  ﻿À présent, vous allez créer un principal du service pour l'application. Tapez la commande suivante, puis appuyez sur Entrée :

        New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Cette étape affiche des informations similaires aux suivantes :

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Website
        ServicePrincipalNames : {OrgIdFederationSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    <div class="dev-callout"><strong>Remarque</strong><p>Enregistrez ce r&eacute;sultat, surtout la cl&eacute; sym&eacute;trique g&eacute;n&eacute;r&eacute;e. Cette cl&eacute; est uniquement affich&eacute;e pendant la cr&eacute;ation du principal du service et vous ne pourrez pas la r&eacute;cup&eacute;rer apr&egrave;s cela. Les autres valeurs sont requises pour l'utilisation de l'API Graph pour lire et &eacute;crire des informations dans l'annuaire.</p></div>

6.  Dans la dernière étape, vous allez définir l'URL de réponse de votre application. Celle-ci reçoit les réponses après les tentatives d'authentification. Tapez les commandes suivantes, puis appuyez sur Entrée :

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

L'application web est à présent configurée dans l'annuaire et peut être utilisée pour l'authentification unique web par les employés de la société.

## <a name="protectapp"></a>Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés

Cette étape vous indique comment ajouter la prise en charge de la connexion fédérée grâce à Windows Identity Foundation (WIF). Vous allez également ajouter une page de connexion et configurer l'approbation entre l'application et le client d'annuaire.

1.  Dans Visual Studio, cliquez avec le bouton droit sur le projet **OrgIdFederationSample**, puis sélectionnez **« Ajouter la référence STS... »**. Cette option a été ajoutée dans le menu contextuel lors de l'installation du SDK WIF.
2.  Dans la boîte de dialogue **Utilitaire de fédération**, sous **URI d'application**, vous devez indiquer l'URI en respectant le format suivant :

        spn:<Your AppPrincipalId>@<Your Directory Domain>

    **spn** indique que l'URI est un nom de principal de service, **Your AppPrincpalId** est la valeur GUID **AppPrincipalId** générée lors de la création d'un principal de service. **Your Directory Domain** est le domaine \*.onmicrosoft.com pour le client d'annuaire. Dans cet exemple d'application, l'URI d'application complète est la suivante :

        spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

    Après avoir saisi l'URI d'application, cliquez sur **Suivant**.

3.  Un avertissement vous indique que l'application n'est pas hébergée sur une connexion https sécurisée. Ceci est dû au fait que l'utilitaire de fédération ne reconnaît pas le format **spn:**, mais l'application continue malgré tout d'utiliser une connexion https sécurisée. Cliquez sur **Oui** pour continuer.

4.  Sur la page suivante de l'utilitaire de fédération, sélectionnez **Utiliser un service STS existant**, puis, sous **Emplacement de document de métadonnées STS WS-Federation**, entrez l'URL du document de métadonnées WS-Federation, au format suivant :

        https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

    Pour cette application, l'emplacement de métadonnées WS-Federation est indiqué comme suit :

        https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

    Après avoir saisi l'emplacement de métadonnées, cliquez sur **Suivant**.

5.  Sur la page suivante de l'utilitaire de Fédération, sélectionnez **Désactiver la validation de chaîne de certificats**, puis cliquez sur **Suivant**.

6.  Sur la page suivante de l'utilitaire de Fédération, sélectionnez **Aucun chiffrement**, puis cliquez sur **Suivant**.

7.  La page suivante de l'utilitaire de fédération affiche les demandes fournies par le service d'émission de jeton de sécurité (STS). Examinez les demandes, puis cliquez sur **Suivant**.

8.  Vous allez ensuite configurer IIS (Internet Information Services) afin que SSL soit pris en charge dans votre environnement de développement. Pour ouvrir le Gestionnaire des services Internet, entrez *inetmgr* dans l'invite **Exécuter**.

9.  Dans le Gestionnaire des services Internet, développez le dossier **Sites** dans le menu de gauche, puis cliquez sur **Page d'accueil du site web par défaut**. Dans le menu **Actions** situé à droite, cliquez sur **Liaisons...**.

10. Dans la boîte de dialogue **Liaisons de site**, cliquez sur **Ajouter**. Dans la boîte de dialogue **Ajouter la liaison de site**, définissez la liste déroulante **Type** sur ***https***. Ensuite, sous **Certificat SSL**, sélectionnez **Certificat de développement IIS Express**. Cliquez sur **OK**.

11. Dans le Gestionnaire des services Internet, cliquez sur **Pools d'applications** dans le menu de gauche, puis sélectionnez l'entrée **ASP.NET v4.0** dans la liste. Cliquez ensuite sur **Paramètres avancés** dans le menu **Actions** situé à droite.

12. Dans la boîte de dialogue **Paramètres avancés**, définissez la propriété **Charger le profil utilisateur** sur **true**. Cliquez sur **OK**.

13. Dans Visual Studio, ouvrez le fichier **Web.config** de l'**Explorateur de solutions** à la racine de votre projet.

14. Dans le fichier **Web.config**, recherchez la section **wsFederation**, puis ajoutez un attribut **reply** avec une valeur identique à la variable **$replyUrl** définie lors de la création du principal de service. Par exemple :

        <wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Ajoutez un nœud **httpRuntime** dans la section **system.web** avec l'attribut **requestValidationMode** défini sur **2.0**. Par exemple :

        <system.web>
            <httpRuntime requestValidationMode="2.0" /> 
            ...

    Enregistrez le fichier **Web.config**.

16. L'authentification pour l'application web étant maintenant activée, la page **Index** doit présenter les demandes d'un utilisateur authentifié. Ouvrez le fichier **Index.cshtml** (dans le dossier **Home**, sous le dossier **Views**).

17. Ajoutez l'extrait de code suivant au fichier **Index.cshtml** avant de l'enregistrer :

        <p>
            @if (User.Identity.IsAuthenticated)
            {
            <ul>
                @foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
                {
                    <li>@claim</li>
                }
            </ul>
            }
        </p> 

18. Après avoir enregistré les modifications apportées au fichier **Index.cshtml**, appuyez sur **F5** pour exécuter l'application. Vous êtes alors redirigé vers la page du fournisseur d'identité Office 365, où vous pouvez vous connecter à l'aide de vos informations d'identification de client d'annuaire. Par exemple, [\*thomas.levesque@awesomecomputers.onmicrosoft.com\*][\*thomas.levesque@awesomecomputers.onmicrosoft.com\*].

19. Après vous être connecté avec vos informations d'identification, vous êtes redirigé vers la page d'index de votre contrôleur d'accueil, là où s'affichent les demandes de votre compte. Ceci confirme que vous êtes bien connecté à l'application via l'authentification unique proposée par Azure Active Directory.

## <a name="readdata"></a>Étape 4 : lecture des données de l'annuaire avec l'API Graph

Cette étape montre comment utiliser l'API Graph pour vous connecter à votre client Azure Active Directory et lire les données associées. Pour vous aider à prendre en main l'API Graph, téléchargez l'application ASP.NET suivante : [Exemple d'application avec une prise en charge d'écriture pour l'API Graph][Exemple d'application avec une prise en charge d'écriture pour l'API Graph]. Cette application contient des méthodes d'assistance qui facilitent l'authentification et la création de requêtes pour l'API Graph.

Vous allez également ajouter des autorisations au principal du service de votre application créé durant l'étape 2. Pour ajouter ces autorisations, vous aurez besoin de la valeur AppPrincipalId.

1.  Téléchargez et décompressez l'exemple d'application dans le dossier de votre choix.
2.  Avant de pouvoir utiliser l'exemple de code, vous devez fournir des autorisations supplémentaires au principal du service. Celles-ci permettront au principal du service de lire vos données en utilisant l'API Graph. Dans le menu **Démarrer**, exécutez la console **Module Microsoft Online Services pour Windows PowerShell**.
3.  Vous allez octroyer des autorisations de lecture au principal du service en l'ajoutant au rôle Administrateur de prise en charge du service. Pour plus d'informations sur l'attribution de rôles au principal du service, consultez la page [Contrôle d'accès en fonction du rôle pour l'API Graph][Contrôle d'accès en fonction du rôle pour l'API Graph]. Tapez la commande suivante, puis appuyez sur Entrée :

        Add-MsolRoleMember -RoleMemberType "ServicePrincipal" -RoleName "Service Support Administrator" -RoleMemberObjectId $appPrincipal.ObjectId 

4.  La variable **$appPrincipal.ObjectId** est l'objectId du principal du service. Vous pouvez l'obtenir en entrant la commande suivante, puis en appuyant sur Entrée :

        Get-MsolServicePrincipal -AppPrincipalId 7829c758-2bef-43df-a685-717089474505 

    <div class="dev-callout"><strong>Remarque</strong><p>La valeur **AppPrincipalId pr&eacute;c&eacute;dente a &eacute;t&eacute; renvoy&eacute;e lorsque vous avez cr&eacute;&eacute; le principal du service dans l'&eacute;tape&nbsp;2.</p></div>

5.  Après avoir ajouté le rôle dans le principal du service, démarrez Visual Studio, puis ouvrez le fichier **Web.config** racine de l'exemple d'application.

6.  Recherchez la section *\<configuration\>* dans le fichier **Web.config**, puis accédez à la section *\<appSettings\>*. Remplacez les valeurs des clés *TenantDomainName*, *AppPrincipalId* et *SymmetricKey* par celles du principal du service. Par exemple :

        <appSettings> 
            ...
            <add key="TenantDomainName" value="awesomecomputers.onmicrosoft.com"/> 
            ...
            <add key="AppPrincipalId" value="7829c758-2bef-43df-a685-717089474505"/>     
            ...
            <add key="SymmetricKey" value="qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=" /> 
            ...
        </appSettings>

7.  Enregistrez le fichier **Web.config**, puis appuyez sur **F5** pour exécuter l'application.

8.  L'exemple d'application affiche un menu permettant d'accéder à tous vos utilisateurs, aux administrateurs de la société, etc. Le fait de cliquer sur l'un des liens renvoie les informations stockées dans votre client en utilisant l'API Graph.

## <a name="summary"></a>Résumé

Ce didacticiel vous a indiqué comment créer et configurer une unique application cliente utilisant les fonctionnalités d'authentification unique d'-Azure Active Directory. De plus, vous avez accédé aux données d'annuaire du client en utilisant l'API Graph. Nous vous recommandons d'explorer l'exemple d'application pour comprendre comment tirer parti de l'API Graph dans votre propre application.

Pour en savoir plus sur l'API Graph, [consultez ces informations sur MSDN][consultez ces informations sur MSDN]. Vous pouvez également créer des applications mutualisées pour Azure Active Directory en consultant le didacticiel suivant : [Développement d'une application cloud mutualisée avec Azure Active Directory][Développement d'une application cloud mutualisée avec Azure Active Directory].

  [ASP.NET MVC 3]: http://www.microsoft.com/fr-fr/download/details.aspx?id=4211
  [Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/fr-fr/download/details.aspx?id=17331
  [Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/fr-fr/download/details.aspx?id=4451
  [WCF Data Services pour OData V3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Cmdlets PowerShell pour Office 365]: http://onlinehelp.microsoft.com/fr-fr/office365-enterprises/ff652560.aspx
  [Introduction]: #introduction
  [Étape 1 : création d'une application ASP.NET MVC]: #createapp
  [Étape 2 : configuration de l'application dans le client d'annuaire d'une société]: #provisionapp
  [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]: #protectapp
  [Étape 4 : lecture des données de l'annuaire avec l'API Graph]: #readdata
  [Résumé]: #summary
  [\*thomas.levesque@awesomecomputers.onmicrosoft.com\*]: mailto:*john.doe@awesomecomputers.onmicrosoft.com*
  [Exemple d'application avec une prise en charge d'écriture pour l'API Graph]: http://code.msdn.microsoft.com/Write-Sample-App-for-79e55502
  [Contrôle d'accès en fonction du rôle pour l'API Graph]: http://msdn.microsoft.com/fr-fr/library/hh974466.aspx
  [consultez ces informations sur MSDN]: http://msdn.microsoft.com/fr-fr/library/hh974476.aspx
  [Développement d'une application cloud mutualisée avec Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
