---
title: Utilisation de l’API REST pour accéder aux API du service Azure Mobile Engagement
description: Décrit comment utiliser les API REST Mobile Engagement pour accéder aux API du service Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/07/2016
ms.author: wesmc;ricksal

---
# Utilisation de REST pour accéder aux API du service Azure Mobile Engagement
Azure Mobile Engagement fournit l’[API REST Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) pour vous permettre de gérer des appareils, des campagnes Push/Reach, etc. Cet exemple utilise l’API REST directement pour créer une campagne Announcement, puis l’active et l’envoie à un ensemble d’appareils.

Si vous ne voulez pas utiliser les API REST directement, nous vous fournissons également un [fichier Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que vous pouvez utiliser avec des outils pour générer des Kits SDK dans votre langue par défaut. Nous vous recommandons d’utiliser l’outil [AutoRest](https://github.com/Azure/AutoRest) pour générer votre Kit SDK à partir de notre fichier Swagger. Nous avons créé un Kit SDK .NET de la même manière qui vous permet d’interagir avec ces API à l’aide d’un wrapper C#. Vous n’êtes pas obligé d’effectuer la négociation de jeton d'authentification et l’actualisation vous-même. Si vous voulez parcourir un exemple similaire qui utilise ce wrapper, consultez [Exemple de Kit de développement logiciel (SDK) .NET de l’API Service](mobile-engagement-dotnet-sdk-service-api.md)

Cet exemple utilise les API REST directement pour créer et activer une campagne Announcement.

## Ajout d’un tag appInfo user\_name à l’application Mobile Engagement
Cet exemple nécessite l’ajout d’un tag app-info à l’application Mobile Engagement. Dans le portail Mobile Engagement de votre application, vous pouvez ajouter le tag en cliquant sur **Paramètres** > **Tag (app-info)** > **Nouveau tag (app-info)**. Ajoutez le nouveau tag nommé **user\_name** sous forme de **Chaîne**.

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)

Si vous avez suivi la procédure décrite dans [Prise en main d’Azure Mobile Engagement pour les applications universelles Windows](mobile-engagement-windows-store-dotnet-get-started.md), vous pouvez tester l’envoi du tag **user\_name** en remplaçant simplement `OnNavigatedTo()` dans votre classe `MainPage` pour envoyer le tag app-info similaire au code suivant :

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }



## Création de l’application API Service
1. Pour commencer, vous avez besoin de quatre paramètres que nous utilisons dans cet exemple. Ces paramètres sont **SubscriptionId**, **TenantId**, **ApplicationId** et **Secret**. Pour obtenir ces paramètres d’authentification, il est recommandé d’utiliser l’approche de script PowerShell mentionnée dans la section *Installation unique (à l’aide d’un script)* du didacticiel [Authentification](mobile-engagement-api-authentication.md#authentication).
2. Nous allons utiliser une application console Windows simple pour illustrer l’utilisation des API Service REST pour créer et activer une campagne Announcement. Ouvrez Visual Studio et créez une **application console**.
3. Ensuite, ajoutez le package NuGet **Newtonsoft.Json** à votre projet.
4. Dans le fichier `Program.cs`, ajoutez les instructions `using` suivantes pour les espaces de noms suivants :
   
        using System.IO;
        using System.Net;
        using Newtonsoft.Json.Linq;
        using Newtonsoft.Json;
5. Vous devez définir ensuite les constantes suivantes dans la classe `Program`. Ces dernières sont utilisées pour l’authentification et l’interaction avec l’application Mobile Engagement dans laquelle vous créez la campagne Announcement :

        // Parameters needed for authentication of API calls.
        // These are returned from the PowerShell script in the authentication tutorial. 
        // https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

        // The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/fr-FR/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
        // Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

        // New campaign id returned from creating the new campaign and used to activate and push the campaign
        // a set of devices.
        static String NewCampaignID = null;

        // This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


1. Ajoutez les deux méthodes suivantes à la classe `Program`. Elles servent à exécuter les API REST et afficher les réponses envoyées à la console.
   
        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;
   
            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;
   
            if (authToken != null)
                request.Headers.Add("Authorization", authToken);
   
            if (headers != null)
            {
                request.Headers.Add(headers);
            }
   
            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);
   
                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }
   
            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
   
            return response;
        }

        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

1. Ajoutez le code suivant à votre méthode `Main` pour générer un jeton d’authentification avec les paramètres d’authentification que vous avez collectés :
   
        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************
   
        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);
   
        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }
2. Nous disposons à présent d’un jeton d’authentification valide, nous pouvons donc créer une campagne Reach à l’aide de l’API REST [Create campaign](https://msdn.microsoft.com/library/azure/mt683742.aspx). La nouvelle campagne est une campagne d’annonces **AnyTime** et **Notification-only** simple avec un titre et un message. Il s’agit d’une campagne push manuelle comme l’illustre la capture d’écran suivante. Cela signifie qu’elle est transmise uniquement à l’aide des API.

    ![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

    Ajoutez le code suivant à votre méthode `Main` pour créer la campagne d’annonces :

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ "name": "BirthdayCoupon", " +
                                        ""type": "only_notif", " +
                                        ""deliveryTime": "any", " +
                                        ""notificationType": "popup", " +
                                        ""pushMode":"manual", " +
                                        ""notificationTitle": "Happy Birthday ${user_name}", " +
                                        ""notificationMessage": "Take extra 10% off on your orders today!"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


1. La campagne doit être activée pour pouvoir être envoyée sur un appareil. Nous avons enregistré l’ID de la nouvelle campagne dans la variable `NewCampaignID`. Nous allons utiliser cette variable comme paramètre de chemin d’URI pour activer la campagne à l’aide de l’API REST [Activate campaign](https://msdn.microsoft.com/library/azure/mt683745.aspx). Cette opération doit changer l’état de la campagne en **planifiée** même si elle est envoyée uniquement manuellement avec les API.
   
    Ajoutez le code suivant à votre méthode `Main` pour activer la campagne d’annonces :
   
        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************
   
        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";
   
        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);
   
        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }
2. Pour envoyer la campagne, nous devons fournir les ID d’appareil des utilisateurs qui doivent recevoir la notification. Nous utilisons l’API REST [Query devices](https://msdn.microsoft.com/library/azure/mt683826.aspx) pour obtenir tous les ID d’appareil. Nous ajoutons chaque ID d’appareil à la liste s’il est associé au tag appInfo **user\_name**.
   
   Ajoutez le code suivant à votre méthode `Main` pour obtenir tous les ID d’appareil et remplir la liste deviceList :
   
       //************************************************************************
       //*** Now that the manualPush campaign is activated, get the deviceIds ***
       //*** that you want to trigger the push campaign on.                   ***
       //************************************************************************
   
       String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                 "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                  Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";
   
       Console.WriteLine("Getting device IDs...\n");
       HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
       Stream devicesStream = devicesResponse.GetResponseStream();
       StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
       dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
       devicesReader.Close();
       devicesStream.Close();
       DisplayResponse(devicesData, devicesResponse);
   
       if (devicesResponse.StatusCode == HttpStatusCode.OK)
       {
           Console.WriteLine("Got devices.");
           foreach (dynamic device in devicesData.value)
           {
               // Just adding all in this example
               if (device.appInfo.user_name != null)
               {
                   Console.WriteLine("Adding device for push campaign...");
                   Console.WriteLine("Device ID    : " + device.deviceId);
                   Console.WriteLine("user_name    : " + device.appInfo.user_name);
                   deviceList.Add((String)device.deviceId);
               }
           }
           Console.WriteLine();
       }
       else
       {
           Console.WriteLine("*** Failed to get devices.\n");
           return;
       }
3. Enfin, nous envoyons la campagne à tous les ID d’appareil de notre liste à l’aide de l’API REST [Push campaign](https://msdn.microsoft.com/library/azure/mt683734.aspx). Il s’agit d’une notification **dans l’application**. L’application doit donc être exécutée sur l’appareil pour qu’elle soit reçue par l’utilisateur.
   
   Ajoutez le code suivant à votre méthode `Main` pour envoyer la campagne sur les appareils de la liste deviceList :
   
       //**************************************************************
       //*** Trigger the manualPush campaign on the desired devices ***
       //**************************************************************
   
       String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                 "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                  Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                  "/push?api-version=2014-12-01";
   
       Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
       String deviceIds = "{"deviceIds":" + JsonConvert.SerializeObject(deviceList) + "}";
       Console.WriteLine("\n" + deviceIds + "\n");
       HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
       Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
       StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
       dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
       pushDevicesReader.Close();
       pushDevicesStream.Close();
       DisplayResponse(pushDevicesData, pushDevicesResponse);
   
       if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
       {
           Console.WriteLine("Triggered push on new campaign.\n");
       }
       else
       {
           Console.WriteLine("*** Failed to push campaign to the device list.\n");
       }
4. Générez et exécutez votre application console. Le résultat doit ressembler à ce qui suit :

        C:\Users\Wesley\AzME_Service_API_Rest\test.exe

        Getting authorization token...

        HTTP Status 200 : OK
        {
          "token_type": "Bearer",
          "expires_in": "3599",
          "expires_on": "1458085162",
          "not_before": "1458081262",
          "resource": "https://management.core.windows.net/",
          "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
        b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
        NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
        MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
        F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
        hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
        }

        Got authorization token...
        Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
        aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
        Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
        OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
        iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
        vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H

        Creating new campaign...

        HTTP Status 201 : Created
        {
          "id": 24,
          "state": "draft"
        }

        Created new campaign...
        New Campaign Id    : 24

        Activating new campaign (ID : 24)...

        HTTP Status 200 : OK
        {
          "id": 24,
          "state": "scheduled"
        }

        Activated new campaign...
        New Campaign State : scheduled

        Getting device IDs...

        HTTP Status 200 : OK
        {
          "value": [
            {
              "meta": {
                "lastSeen": 1458080534371,
                "firstSeen": 1458080534371,
                "lastLocation": 1458081389617,
                "lastInfo": 1458080571603
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
            },
            {
              "meta": {
                "lastSeen": 1457990584698,
                "firstSeen": 1457949384025,
                "lastLocation": 1457990914895,
                "lastInfo": 1457990584597
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "302486644890e26045884ee5aa0619ec"
            }
          ]
        }

        Got devices.
        Adding device for push campaign...
        Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
        user_name    : Wesley
        Adding device for push campaign...
        Device ID    : 302486644890e26045884ee5aa0619ec
        user_name    : Wesley

        Triggering push for new campaign (ID : 24)...


        {"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}

        HTTP Status 200 : OK
        {
          "invalidDeviceIds": []
        }

        Triggered push on new campaign.



<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0713_2016-->