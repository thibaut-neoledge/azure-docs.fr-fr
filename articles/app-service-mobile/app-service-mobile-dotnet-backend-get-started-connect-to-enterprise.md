<properties
	pageTitle="Connexion d'une application mobile à une solution SaaS d'entreprise | Microsoft Azure"
	description="Découvrez comment effectuer des appels à des ressources d'entreprise telles que SharePoint Online"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="06/19/2015"
	ms.author="mahender"/>

# Connecter une application mobile aux API SaaS

Dans ce didacticiel, vous connecterez votre application mobile à une solution SaaS (software-as-a-service) d'entreprise. Vous mettrez à jour l’application à partir de la page [Authentification de votre application avec le service d’authentification unique de la bibliothèque d’authentification Azure Active Directory] pour créer un document Microsoft Word dans SharePoint Online, chaque fois qu’un nouvel élément TodoItem est ajouté.

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Un abonnement actif à [SharePoint Online]
* Assimilation du didacticiel [Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory]. Vous devez utiliser le client fourni par votre abonnement SharePoint.

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-mobile-to-web-and-api.md)]

## <a name="configure-permissions"></a>Configuration de votre application pour un accès délégué à SharePoint
Par défaut, le jeton que vous recevez d'AAD a des autorisations limitées. Pour accéder à une ressource ou une application SaaS tierce telle que SharePoint Online, vous devez l'autoriser explicitement.

1. Dans la section **Active Directory** du [portail de gestion Azure], sélectionnez votre client. Accédez à l'application web que vous avez créée pour le service App Service.

2. Sous l'onglet **Configurer**, faites défiler la page jusqu'à la section Autorisations pour d'autres applications. Sélectionnez **Office 365 SharePoint Online** et accordez l'autorisation déléguée **Modifier ou supprimer les fichiers des utilisateurs**. Cliquez ensuite sur **Enregistrer**.

    ![][1]

Vous avez maintenant configuré Azure AD pour émettre un jeton d'accès SharePoint au service App Service.

## <a name="store-credentials"></a>Ajout d’informations SharePoint à votre application mobile

Pour appeler SharePoint, vous devez spécifier les points de terminaison avec lesquels l'application mobile a besoin de communiquer. Vous devez également être capable de prouver l'identité de votre service d'application avec une paire ID client/clé secrète. Vous avez déjà obtenu et stocké l'ID client pour le service d'application pendant la configuration de la connexion AAD. Comme il s'agit d'informations d'identification sensibles, vous ne devez pas les stocker en clair dans votre code. Définissez plutôt ces valeurs en tant que paramètres d'application pour votre site Code de l'application mobile.

1. Revenez sous l'onglet Applications AAD de votre client et sélectionnez l'application web pour votre service d'application.

2. Sous Configurer, faites défiler l'écran jusqu'à la section Clés. Vous pouvez obtenir une clé secrète client en générant une nouvelle clé. Notez que lorsque vous créez une clé et quittez la page, il n'est plus possible de l'éliminer du portail. Une fois cette valeur créée, copiez-la et enregistrez-la dans un emplacement sûr. Sélectionnez une durée pour votre clé, puis cliquez sur Enregistrer et faites une copie de la valeur obtenue.

3. Dans la section Code de l'application mobile du portail de gestion, accédez à l'onglet Configurer puis à la section Paramètres de l’application. Vous pouvez fournir ici une paire clé/valeur qui vous aidera à référencer les informations d'identification nécessaires.

* Définissez SP\_Authority sur le point de terminaison d'autorité pour votre client AAD. Cette valeur doit être identique à la valeur d'autorité utilisée pour votre application cliente. Elle aura la forme suivante : `https://login.windows.net/contoso.onmicrosoft.com`

* Définissez SP\_ClientSecret sur la valeur de la clé secrète client que vous avez obtenue précédemment.

* Définissez SP\_SharePointURL sur l'URL de votre site SharePoint. Il doit être de la forme `https://contoso-my.sharepoint.com`

Vous pouvez obtenir de nouveau ces valeurs dans votre code avec ApiServices.Settings.

## <a name="obtain-token"></a>Obtention d'un jeton d'accès et appel de l'API SharePoint

Pour accéder à SharePoint, vous avez besoin d'un jeton d'accès spécial avec SharePoint comme public cible. Pour obtenir ce jeton, vous devez effectuer un rappel dans Azure AD avec l'identité du service App Service et le jeton qui a été émis pour l'utilisateur.

1. Ouvrez votre projet Code de l'application mobile dans Visual Studio.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. Dans le gestionnaire de packages NuGet, cliquez sur **En ligne**. Entrez **Microsoft.Azure.Mobile.Server.AppService** comme terme de recherche. Cliquez ensuite sur **Installer** pour installer le package [Mobile Apps .NET Backend App Service Extension]. Ce package fournit des méthodes d’extension pour l’utilisation des informations sur l’utilisateur actuellement connecté.

2. Dans votre projet Code de l’application mobile, créez une classe appelée SharePointUploadContext. Ajoutez une instruction `using Microsoft.Azure.Mobile.Server.AppService;` dans le fichier. Ajoutez ensuite le code suivant à la classe :

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

3. Créez maintenant une méthode pour ajouter le fichier à la bibliothèque de documents de l'utilisateur :

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

## <a name="create-document"></a>Création et téléchargement d’un document Word

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

2. Remplacez PostTodoItem par le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Testez l’application

1. Publiez les modifications sur le serveur principal, puis exécutez votre application cliente. Connectez-vous lorsque vous y êtes invité et insérez un nouveau TodoItem.

2. Accédez à votre site SharePoint et connectez-vous avec le même nom d'utilisateur.

3. Sélectionnez l'onglet OneDrive. Dans le dossier Documents, apparaît normalement un document Word dont le titre est un identificateur global unique (GUID). Lorsque vous l'ouvrez, vous devez normalement trouver le texte de votre TodoItem.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/fr-FR/sharepoint/
[Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[Mobile Apps .NET Backend App Service Extension]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.AppService/

<!---HONumber=Oct15_HO4-->