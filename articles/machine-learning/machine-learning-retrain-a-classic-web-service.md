<properties
    pageTitle="Reformer un service web classique | Microsoft Azure"
    description="Apprenez à reformer un modèle par programmation et à mettre à jour le service web de sorte qu’il utilise le modèle reformé dans Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-donglo"/>


# <a name="retrain-a-classic-web-service"></a>Reformer un service web classique

Le service web prédictif que vous avez déployé est le point de terminaison de notation par défaut. Les points de terminaison par défaut sont toujours synchronisés avec l’expérience originale d’apprentissage et de notation. Par conséquent, le modèle entraîné du point de terminaison par défaut ne peut pas être remplacé. Pour reformer le service web, vous devez ajouter un nouveau point de terminaison au service web. 

>[AZURE.NOTE] Cette procédure pas à pas suppose que vous avez créé le service web en procédant de la manière décrite dans [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md).


## <a name="add-a-new-endpoint"></a>Ajouter un nouveau point de terminaison
 
Le service web prédictif que vous avez déployé contient un point de terminaison de notation par défaut qui est synchronisé avec le modèle formé pour les expériences de formation et de notation d’origine. Pour mettre à jour votre service web avec un nouveau modèle formé, vous devez créer un nouveau point de terminaison de notation. 

Pour créer un nouveau point de terminaison de notation, sur le service web prédictif pouvant être mis à jour avec le modèle entraîné :

>[AZURE.NOTE] Veillez à ajouter le point de terminaison au service web prédictif et non au service web d’apprentissage. Si vous avez correctement déployé à la fois un service web prédictif et un service web de formation, vous devez voir deux services web distincts répertoriés. Le service web prédictif doit se terminer par « [exp. prédictive] ».

Pour ajouter un nouveau point de terminaison à un service web, trois options s’offrent à vous :

3. Par programmation
1. Utilisation du portail de services web Microsoft Azure
2. Utilisation du portail Azure Classic

### <a name="programmatically-add-an-endpoint"></a>Ajouter un point de terminaison par programmation

Vous pouvez ajouter des points de terminaison de notation à l’aide de l’exemple de code fourni dans ce [référentiel GitHub](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Utiliser le portail de services web Microsoft Azure pour ajouter un point de terminaison

1. Dans Machine Learning Studio, dans la colonne de navigation de gauche, cliquez sur Services web.
2. En bas du tableau de bord de services web, cliquez sur **Manage endpoints preview**(Gérer les points de terminaison - version préliminaire).
3. Cliquez sur **Add**.
4. Tapez un nom et une description pour le point de terminaison. Sélectionnez le niveau de journalisation et activez les exemples de données si nécessaire. Pour plus d’informations sur la journalisation, consultez [Activation de la journalisation pour les services web de Machine Learning](machine-learning-web-services-logging.md).

### <a name="use-the-azure-classic-portal-to-add-an-endpoint"></a>Utiliser le portail Azure Classic pour ajouter un point de terminaison

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Dans le menu de gauche, cliquez sur **Machine Learning**.
3. Sous Nom, cliquez sur votre espace de travail, puis sur **Services web**.
4. Sous Nom, cliquez sur **Modèle de recensement [exp. prédictive]**.
5. En bas de la page, cliquez sur **Ajouter un point de terminaison**. Pour plus d’informations sur l’ajout de points de terminaison, consultez [Création de points de terminaison](machine-learning-create-endpoint.md). 

## <a name="update-the-added-endpoint’s-trained-model"></a>Mettre à jour le modèle entraîné du point de terminaison ajouté

Pour terminer le processus de nouvel entraînement, vous devez mettre à jour le modèle entraîné du nouveau point de terminaison que vous avez ajouté.

* Si vous avez ajouté le nouveau point de terminaison à l’aide du portail Azure Classic, vous pouvez cliquer sur le nom du nouveau point de terminaison dans le portail, puis sur le lien **UpdateResource** pour obtenir l’URL dont vous avez besoin pour mettre à jour le modèle du point de terminaison.
* Si vous avez ajouté le point de terminaison à l’aide de l’exemple de code, cela inclut l’emplacement de l’URL d’aide identifiée par la valeur *HelpLocationURL* dans la sortie.
 
Pour récupérer l’URL du chemin d’accès :

1.  Copiez et collez l’URL dans votre navigateur.
2.  Cliquez sur le lien Mettre à jour les ressources.
3.  Copiez l’URL de la publication de la requête PATCH. Par exemple :

        PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Vous pouvez maintenant utiliser le modèle entraîné pour mettre à jour le point de terminaison de notation que vous avez créé précédemment.

L’exemple de code suivant montre comment utiliser les éléments *BaseLocation*, *RelativeLocation*, *SasBlobToken* et l’URL d’application de correctifs pour mettre à jour le point de terminaison.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };
    
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);
    
            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);
    
                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }
    
                // Do what you want with a successful response here.
            }
        }
    }

L’*apiKey* et l’*endpointUrl* pour l’appel sont figurent sur le tableau de bord du point de terminaison.

La valeur du paramètre *Name* dans *Ressources* doit correspondre au nom de ressource du modèle formé enregistré dans l’expérience prédictive. Pour obtenir le nom de la ressource :

1.  Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2.  Dans le menu de gauche, cliquez sur **Machine Learning**.
3.  Sous Nom, cliquez sur votre espace de travail, puis sur **Services web**.
4.  Sous Nom, cliquez sur **Modèle de recensement [exp. prédictive]**.
5.  Cliquez sur le nouveau point de terminaison que vous avez ajouté.
6.  Dans le tableau de bord du point de terminaison, cliquez sur **Mettre à jour les ressources**.
7.  Dans la page Documentation de l’API Mettre à jour la ressource du service web, vous trouverez le **Nom de la ressource** sous **Ressources actualisables**.

Si votre jeton SAP expire avant la fin de la mise à jour du point de terminaison, vous devez effectuer une opération GET avec l’ID du travail pour obtenir un nouveau jeton.

Lorsque le code a été exécuté avec succès, le nouveau point de terminaison doit commencer à utiliser le modèle de nouveau entraîné après environ 30 secondes.

##<a name="summary"></a>Résumé

À l’aide des API Retraining, vous pouvez mettre à jour le modèle entraîné d’un service web prédictif pour prendre en charge des scénarios tels que :

* Nouvel apprentissage périodique d’un modèle avec de nouvelles données.
* Distribution d’un modèle auprès des clients dans le but de leur permettre d’effectuer à nouveau l’apprentissage du modèle avec leurs propres données.

Étapes suivantes

[Résolution des problèmes de reformation d’un service web Azure Machine Learning classique](machine-learning-troubleshooting-retraining-models.md)


<!--HONumber=Oct16_HO2-->


