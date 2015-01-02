<properties urlDisplayName="" pageTitle="Authentification unique web avec .NET et Azure Active Directory" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Web Single Sign-On with .NET and Azure Active Directory" authors="" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="mbaldwin" />






# Authentification unique web avec .NET et Azure Active Directory

<h2><a name="introduction"></a>Introduction</h2>

Ce didacticiel explique comment tirer parti d'Azure Active Directory pour activer l'authentification unique pour les utilisateurs des clients Office 365. Vous apprendrez à :

* configurer l'application web dans le client d'une société ;
* protéger l'application avec WS-Federation.

<h3>Configuration requise</h3>
Les éléments de développement suivants sont requis pour cette procédure pas à pas :

* Visual Studio 2010 SP1
* Microsoft .NET Framework 4.0
* [ASP.NET MVC 3]
* [Windows Identity Foundation 1.0 Runtime]
* [Windows Identity Foundation 3.5 SDK]
* IIS (Internet Information Services) 7.5 avec SSL activé
* Windows PowerShell
* [Cmdlets PowerShell pour Office 365]

<h3>Sommaire</h3>
* [Introduction][]
* [Étape 1 : création d'une application ASP.NET MVC][]
* [Étape 2 : configuration de l'application dans le client d'annuaire d'une société][]
* [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés][]
* [Résumé][]

<h2><a name="createapp"></a>Étape 1 : Création d'une application ASP.NET MVC</h2>
Cette étape décrit la création d'une application ASP.NET MVC 3 simple qui représentera une source protégée. L'accès à cette ressource sera accordé via une authentification fédérée gérée par le service d'émission de jeton de sécurité (STS) d'une société, qui est décrit ultérieurement dans ce didacticiel.

1. Ouvrez Visual Studio en tant qu'administrateur.
2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**. 
3. Dans le menu du modèle, à gauche, sélectionnez **Web**, puis cliquez sur **Application web ASP.NET MVC 3**. Nommez le projet *OrgIdFederationSample*.
4. Dans la boîte de dialogue **Nouveau projet ASP.NET MVC 3**, sélectionnez le modèle **Vide**, puis cliquez sur **OK**.
5. Visual Studio génère le nouveau projet. Cliquez ensuite avec le bouton droit sur le dossier **Contrôleurs** dans l'**Explorateur de solutions**, cliquez sur **Ajouter**, puis sur **Contrôleur**.
6. Dans la boîte de dialogue **Ajouter un contrôleur**, nommez le contrôleur *HomeController.cs*, puis cliquez sur **Ajouter**.
7. Le fichier **HomeController.cs** est créé et s'ouvre. Dans le fichier de code, cliquez avec le bouton droit sur la méthode ***Index()***, puis cliquez sur **Ajouter une vue...**.. 
8. Dans la boîte de dialogue **Ajouter une vue**, cliquez sur **OK**. Un fichier **Index.cshtml** est créé dans un nouveau dossier nommé **Home**. 
9. Cliquez avec le bouton droit sur le projet **OrgIdFederationSample** dans l'**Explorateur de solutions**, puis cliquez sur **Propriétés**.
10. Dans la fenêtre de propriétés, cliquez sur **Web** dans le menu de gauche, puis sélectionnez **Utiliser le serveur web IIS local**. Selon les cas, une boîte de dialogue vous demandant de créer un répertoire virtuel pour le projet peut s'afficher. Cliquez sur **Oui**.
11. Générez et exécutez l'application. La page d'index de votre contrôleur d'accueil s'affiche.

<h2><a name="provisionapp"></a>Étape 2 : configuration de l'application dans le client d'annuaire d'une société</h2>
Cette étape explique comment l'administrateur d'une société utilisant Azure Active Directory met en service l'application MVC dans son client et configure l'authentification unique. Une fois cette étape terminée, les employés de la société peuvent s'authentifier pour accéder à l'application web en utilisant leurs comptes Office 365.

Le processus de configuration commence par la création d'un principal du service pour l'application. Azure Active Directory utilise les principaux du service pour enregistrer et authentifier des applications dans l'annuaire.

1. Si ce n'est pas déjà fait, téléchargez et installez les cmdlets PowerShell pour Office 365.
2. Dans le menu **Démarrer**, exécutez la console **Module Microsoft Online Services pour Windows PowerShell**. Celle-ci fournit un environnement en ligne de commande permettant de configurer des attributs de votre client Office 365, comme la création et la modification des principaux du service.
3. Pour importer le module **MSOnlineExtended** requis, entrez la commande suivante, puis appuyez sur Entrée :

		Import-Module MSOnlineExtended -Force
4. Pour vous connecter à votre annuaire Office 365, vous devez fournir les informations d'identification de l'administrateur de la société. Tapez la commande suivante et appuyez sur Entrée, puis entrez vos informations d'identification lorsque vous y êtes invité :

		Connect-MsolService
5. À présent, vous allez créer un principal du service pour l'application. Tapez la commande suivante, puis appuyez sur Entrée :

		New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
Cette étape affiche des informations similaires aux suivantes :

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
	<div class="dev-callout"><strong>Remarque</strong><p>Enregistrez ce résultat, surtout la clé symétrique générée. Cette clé est uniquement affichée pendant la création du principal du service et vous ne pourrez pas la récupérer après cela. Les autres valeurs sont requises pour l'utilisation de l'API Graph pour lire et écrire des informations dans l'annuaire.</p></div>

6. Dans la dernière étape, vous allez définir l'URL de réponse de votre application. Celle-ci reçoit les réponses après les tentatives d'authentification. Tapez les commandes suivantes, puis appuyez sur Entrée :

		$replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

		Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 
	
L'application web est à présent configurée dans l'annuaire et peut être utilisée pour l'authentification unique web par les employés de la société.

<h2><a name="protectapp"></a>Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés</h2>
Cette étape vous indique comment ajouter la prise en charge de la connexion fédérée grâce à Windows Identity Foundation (WIF). Vous allez également ajouter une page de connexion et configurer l'approbation entre l'application et le client d'annuaire.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **OrgIdFederationSample**, puis sélectionnez **" Ajouter la référence STS... "**. Cette option a été ajoutée dans le menu contextuel lors de l'installation du SDK WIF.
2. Dans la boîte de dialogue **Utilitaire de fédération**, sous **URI d'application**, vous devez indiquer l'URI en respectant le format suivant :

		spn:<Your AppPrincipalId>@<Your Directory Domain>

	**spn** indique que l'URI est un nom de principal de service, **Your AppPrincpalId** est la valeur GUID **AppPrincipalId** générée lors de la création d'un principal de service. **Your Directory Domain** est le domaine *.onmicrosoft.com pour le client d'annuaire. Dans cet exemple d'application, l'URI d'application complète est la suivante :

		spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

	Après avoir saisi l'URI d'application, cliquez sur **Suivant**.

3. Un avertissement vous indique que l'application n'est pas hébergée sur une connexion https sécurisée. Ceci est dû au fait que l'utilitaire de fédération ne reconnaît pas le format **spn:**, mais l'application continue malgré tout d'utiliser une connexion https sécurisée. Cliquez sur **Oui** pour continuer.

4. Sur la page suivante de l'utilitaire de fédération, sélectionnez **Utiliser un service STS existant**, puis, sous **Emplacement de document de métadonnées STS WS-Federation**, entrez l'URL du document de métadonnées WS-Federation, au format suivant :

		https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

	Pour cette application, l'emplacement de métadonnées WS-Federation est indiqué comme suit :

		https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

	Après avoir saisi l'emplacement de métadonnées, cliquez sur **Suivant**.

5. Sur la page suivante de l'utilitaire de fédération, sélectionnez **Désactiver la validation de chaîne de certificats**, puis cliquez sur **Suivant**.

6. Sur la page suivante de l'utilitaire de fédération, sélectionnez **Aucun chiffrement**, puis cliquez sur **Suivant**.

7. La page suivante de l'utilitaire de fédération affiche les demandes fournies par le service d'émission de jeton de sécurité (STS). Examinez les demandes, puis cliquez sur **Suivant**.

8. Vous allez ensuite configurer IIS (Internet Information Services) afin que SSL soit pris en charge dans votre environnement de développement. Pour ouvrir le Gestionnaire des services Internet, entrez *inetmgr* dans l'invite **Exécuter**. 

9. Dans le Gestionnaire des services Internet, développez le dossier **Sites** dans le menu de gauche, puis cliquez sur **Page d'accueil du site web par défaut**. Dans le menu **Actions** situé à droite, cliquez sur **Liaisons...**.

10. Dans la boîte de dialogue **Liaisons de site**, cliquez sur **Ajouter**. Dans la boîte de dialogue **Ajouter la liaison de site**, définissez la liste déroulante **Type** sur ***https***, puis sous **Certificat SSL**, sélectionnez **Certificat de développement IIS Express**. Cliquez sur **OK**.

11. Dans le Gestionnaire des services Internet, cliquez sur **Pools d'applications** dans le menu de gauche, puis sélectionnez l'entrée **ASP.NET v4.0** dans la liste. Cliquez ensuite sur **Paramètres avancés** dans le menu **Actions** situé à droite.

12. Dans la boîte de dialogue **Paramètres avancés**, définissez la propriété **Charger le profil utilisateur** sur **true**. Cliquez sur **OK**.

13. Dans Visual Studio, ouvrez le fichier **Web.config** dans l'**Explorateur de solutions** à la racine de votre projet. 

14. Dans le fichier **Web.config**, recherchez la section **wsFederation**, puis ajoutez un attribut **reply** avec une valeur identique à la variable **$replyUrl** définie lors de la création du principal de service. Par exemple :

		<wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Ajoutez un nœud **httpRuntime** dans la section **system.web** et définissez l'attribut **requestValidationMode** sur **2.0**. Par exemple :

		<system.web>
			<httpRuntime requestValidationMode="2.0" /> 
			...

	Enregistrez le fichier **Web.config**.

16. L'authentification pour l'application web étant maintenant activée, la page **Index** doit présenter les demandes d'un utilisateur authentifié. Ouvrez le fichier **Index.cshtml** (dans le dossier **Home**, sous le dossier **Views**).

17. Ajoutez l'extrait de code suivant au fichier **Index.cshtml** avant de l'enregistrer :

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

18. Après avoir enregistré les modifications apportées au fichier **Index.cshtml**, appuyez sur **F5** pour exécuter l'application. Vous êtes alors redirigé vers la page du fournisseur d'identité Office 365, où vous pouvez vous connecter à l'aide de vos informations d'identification de client d'annuaire. Par exemple, *thomas.levesque@awesomecomputers.onmicrosoft.com*.

19. Après vous être connecté avec vos informations d'identification, vous êtes redirigé vers la page d'index de votre contrôleur d'accueil, là où s'affichent les demandes de votre compte. Ceci confirme que vous êtes bien connecté à l'application via l'authentification unique proposée par Azure Active Directory.

<h2><a name="summary"></a>Résumé</h2>
Ce didacticiel vous a indiqué comment créer et configurer une unique application cliente utilisant les fonctionnalités d'authentification unique d'-Azure Active Directory. Vous pouvez également créer des applications mutualisées pour Azure Active Directory en consultant le didacticiel suivant : [Développement d'une application cloud mutualisée avec Azure Active Directory].

[Introduction]: #introduction
[Étape 1 : création d'une application ASP.NET MVC]: #createapp
[Étape 2 : configuration de l'application dans le client d'annuaire d'une société]: #provisionapp
[Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]: #protectapp
[Résumé]: #summary
[Développement d'une application cloud mutualisée avec Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/fr-fr/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/fr-fr/download/details.aspx?id=17331
[Cmdlets Powershell pour Office 365]: http://onlinehelp.microsoft.com/fr-fr/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/fr-fr/download/details.aspx?id=4211


<!--HONumber=35_1-->
