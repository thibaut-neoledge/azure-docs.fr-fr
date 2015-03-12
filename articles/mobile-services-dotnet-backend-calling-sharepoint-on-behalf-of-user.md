<properties 
	pageTitle="Accès à SharePoint pour l'utilisateur | Centre de développement mobile" 
	description="Découvrez comment appeler SharePoint pour l'utilisateur" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# Accès à SharePoint pour le compte de l'utilisateur

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Cette rubrique explique comment accéder aux API SharePoint pour le compte de l'utilisateur actuellement connecté.</p>
<p>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel. Dans la vidéo, Mat Velloso vous montre comment mettre à jour une application Windows Store pour interagir avec SharePoint Online.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">12:51</span></div>
</div>

Dans ce didacticiel, vous allez mettre à jour l'application du didacticiel " Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory " de manière à créer un document Word dans SharePoint Online lorsqu'un nouveau TodoItem est ajouté.

Ce didacticiel vous familiarise avec les étapes de base suivantes pour activer l'accès " pour le compte de " à SharePoint :

1. [Inscription de votre application pour un accès délégué à SharePoint]
2. [Ajout d'informations SharePoint à votre service mobile]
3. [Obtention d'un jeton d'accès et appel de l'API SharePoint]
4. [Création et téléchargement d'un document Word]
5. [Test de l'application]

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Un abonnement actif à [SharePoint Online]
* Assimilation du didacticiel [Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory]. Vous devez utiliser le client fourni par votre abonnement SharePoint.

## <a name="configure-permissions"></a>Configuration de votre application pour un accès délégué à SharePoint
Par défaut, le jeton que vous recevez d'AAD a des autorisations limitées. Pour accéder à une ressource ou une application SaaS tierce telle que SharePoint Online, vous devez l'autoriser explicitement.

1. Dans la section **Active Directory** du [portail de gestion Azure], sélectionnez votre client. Accédez à l'application web que vous avez créée pour le service mobile.

    ![][0]

2. Sous l'onglet **Configurer**, faites défiler la page jusqu'à la section Autorisations pour d'autres applications. Sélectionnez **Office 365 SharePoint Online** et accordez l'autorisation déléguée **Modifier ou supprimer les fichiers des utilisateurs**. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous avez maintenant configuré AAD pour émettre un jeton d'accès SharePoint au service mobile.

## <a name="store-credentials"></a>Ajout d'informations SharePoint à votre service mobile

Pour appeler SharePoint, vous devez spécifier les points de terminaison avec lesquels le service mobile a besoin de communiquer. Vous devez également être capable de prouver l'identité de votre service mobile avec une paire ID client/clé secrète. Vous avez déjà obtenu et stocké l'ID client pour le service mobile pendant la configuration de la connexion AAD. Comme il s'agit d'informations d'identification sensibles, vous ne devez pas les stocker en clair dans votre code. Définissez plutôt ces valeurs en tant que paramètres d'application pour votre service mobile.

1. Revenez sous l'onglet Applications AAD de votre client et sélectionnez l'application web pour votre service mobile.

2. Sous Configurer, faites défiler l'écran jusqu'à la section Clés. Vous pouvez obtenir une clé secrète client en générant une nouvelle clé. Notez que lorsque vous créez une clé et quittez la page, il n'est plus possible de l'éliminer du portail. Une fois cette valeur créée, copiez-la et enregistrez-la dans un emplacement sûr. Sélectionnez une durée pour votre clé, puis cliquez sur Enregistrer et faites une copie de la valeur obtenue.

    ![][2]

3. Dans la section Mobile Services du portail de gestion, accédez à l'onglet Configurer puis à la section Paramètres de l'application. Vous pouvez fournir ici une paire clé/valeur qui vous aidera à référencer les informations d'identification nécessaires.

    ![][3]

4. Définissez SP_Authority sur le point de terminaison d'autorité pour votre client AAD. Cette valeur doit être identique à la valeur d'autorité utilisée pour votre application cliente. Elle aura la forme suivante : https://login.windows.net/contoso.onmicrosoft.com

5. Définissez SP_ClientSecret sur la valeur de la clé secrète client que vous avez obtenue précédemment.

6. Définissez SP_SharePointURL sur l'URL de votre site SharePoint. Elle aura la forme suivante : https://contoso-my.sharepoint.com

Vous pouvez obtenir de nouveau ces valeurs dans notre code avec ApiServices.Settings.

## <a name="obtain-token"></a>Obtention d'un jeton d'accès et appel de l'API SharePoint

Pour accéder à SharePoint, vous avez besoin d'un jeton d'accès spécial avec SharePoint comme public cible. Pour obtenir ce jeton, vous devez effectuer un rappel dans AAD avec l'identité du service mobile et le jeton qui a été émis pour l'utilisateur.

1. Ouvrez votre projet de service principal Mobile Services dans Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

2. Dans votre projet de service principal Mobile Services, créez une classe appelée SharePointUploadContext. Ajoutez-y le code suivant :

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web"; 
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. Créez maintenant une méthode pour ajouter le fichier à la bibliothèque de documents de l'utilisateur :

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Création et téléchargement d'un document Word

Pour créer un document Word, vous allez utiliser le package NuGet OpenXML. Installez ce package en ouvrant le gestionnaire du package NuGet et en recherchant DocumentFormat.OpenXml.

1. Ajoutez le code suivant à TodoItemController. Vous créez ainsi un document Word basé sur un TodoItem. Le texte du document est le nom de l'élément.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. Remplacez PostTodoItem par le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            
            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);
            
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Test de l'application

1. Publiez les modifications sur le serveur principal, puis exécutez votre application cliente. Connectez-vous lorsque vous y êtes invité et insérez un nouveau TodoItem.

2. Accédez à votre site SharePoint et connectez-vous avec le même nom d'utilisateur.

3. Sélectionnez l'onglet OneDrive. Dans le dossier Documents, apparaît normalement un document Word dont le titre est un identificateur global unique (GUID). Lorsque vous l'ouvrez, vous devez normalement trouver le texte de votre TodoItem.

    ![][4]


<!-- Images. -->

[0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
[1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
[2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
[3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
[4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png

<!-- Anchors. -->

[Inscription de votre application pour un accès délégué à SharePoint]: #configure-permissionss
[Ajout d'informations SharePoint à votre service mobile]: #store-credentials
[Obtention d'un jeton d'accès et appel de l'API SharePoint]: #obtain-token
[Création et téléchargement d'un document Word]: #create-document
[Test de l'application]: #test-application

<!-- URLs. -->
[Portail de gestion Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/fr-fr/sharepoint/
[Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory]: http://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/

<!--HONumber=42-->
