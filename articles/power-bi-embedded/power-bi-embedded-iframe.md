---
title: Utiliser Power BI Embedded avec REST | Microsoft Docs
description: 'Apprenez à utiliser Power BI Embedded avec REST  '
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="how-to-use-power-bi-embedded-with-rest"></a>Utiliser Power BI Embedded avec REST
## <a name="power-bi-embedded:-what-it-is-and-what-it's-for"></a>Power BI Embedded : présentation et objectif
Une vue d’ensemble de Power BI Embedded est décrite sur le [site Power BI Embedded officiel](https://azure.microsoft.com/services/power-bi-embedded/), mais commençons par jeter un coup d’œil rapide avant d’entrer dans les détails de son utilisation avec REST.

C’est très simple. Bien souvent, les éditeurs de logiciels indépendants souhaitent utiliser les visualisations de données dynamiques de [Power BI](https://powerbi.microsoft.com) dans leur propre application en tant que blocs d’interface utilisateur.

Mais, comme vous le savez, l’incorporation de rapports ou de vignettes Power BI sur votre page web est déjà possible sans le service Azure Power BI Embedded, avec **l’API Power BI**. Lorsque vous souhaitez partager vos rapports dans votre organisation, vous pouvez incorporer les rapports avec l’authentification Azure AD. L’utilisateur qui consulte les rapports doit se connecter avec son propre compte Azure AD. Lorsque vous souhaitez partager vos rapports pour tous les utilisateurs (y compris les utilisateurs externes), vous pouvez simplement effectuer l’incorporation avec un accès anonyme.

Mais, vous le voyez, cette solution d’incorporation simple ne répond pas aux besoins de l’application d’un éditeur de logiciels indépendant.
La plupart des applications ISV ont besoin de fournir les données à leurs clients, et pas nécessairement aux utilisateurs de leur propre organisation. Par exemple, si vous proposez un service à la société A et à la société B, les utilisateurs de la société A doivent voir uniquement les données de leur propre société, la société A. Autrement dit, l’architecture mutualisée est nécessaire pour fournir les données.

L’application ISV peut également offrir ses propres méthodes d’authentification, notamment l’authentification par formulaire, l’authentification de base, etc. Dans ce cas, la solution d’incorporation doit collaborer en toute sécurité avec cette méthode d’authentification existante. Il est également nécessaire que les utilisateurs puissent utiliser ces applications ISV sans achat supplémentaire ou acquisition d’une licence d’abonnement Power BI.

 **Power BI Embedded** est précisément conçu pour ces types de scénarios des éditeurs de logiciels indépendants. Maintenant que cette introduction rapide est faite, nous allons pouvoir entrer un peu dans les détails

Vous pouvez utiliser le Kit de développement logiciel (SDK) .NET \(C#) ou Node.js pour créer facilement votre application avec Power BI Embedded. Toutefois, dans cet article, nous expliquerons le flux HTTP \(y compris AuthN) de Power BI sans Kit de développement logiciel (SDK). Si vous comprenez ce flux, vous pouvez générer votre application **avec n’importe quel langage de programmation** et comprendre l’essence même de Power BI Embedded.

## <a name="create-power-bi-workspace-collection,-and-get-access-key-\(provisioning)"></a>Création d’une collection d’espaces de travail Power BI et obtention de la clé d’accès \(approvisionnement)
Power BI Embedded fait partie des services Azure. Seul l’éditeur de logiciels indépendant qui utilise le portail Azure doit payer des frais d’utilisation \(par session d’utilisateur à l’heure). L’utilisateur qui consulte le rapport n’est pas facturé et n’a même pas besoin d’un abonnement Azure.
Avant de commencer le développement de notre application, nous devons créer la **collection d’espaces de travail Power BI** avec le portail Azure.

Chaque espace de travail de Power BI Embedded est l’espace de travail de chaque client (locataire), et nous pouvons ajouter de nombreux espaces de travail à chaque collection d’espaces de travail. La même clé d’accès est utilisée dans chaque collection d’espaces de travail. En effet, la collection d’espaces de travail est la limite de sécurité de Power BI Embedded.

![](media\\power-bi-embedded-iframe\\create-workspace.png)

Une fois que nous avons terminé la création de la collection d’espaces de travail, copions la clé d’accès à partir du portail Azure.

![](media\\power-bi-embedded-iframe\\copy-access-key.png)

> [!NOTE]
> Nous pouvons également configurer la collection d’espaces de travail et obtenir la clé d’accès avec l’API REST. Pour plus d’informations, consultez [API du fournisseur de ressources Power BI](https://msdn.microsoft.com/library/azure/mt712306.aspx).
> 
> 

## <a name="create-.pbix-file-with-power-bi-desktop"></a>Création d’un fichier .pbix avec Power BI Desktop
Ensuite, nous devons créer la connexion de données et les rapports à incorporer.
Pour cette tâche, aucune programmation et aucun code ne sont nécessaires. Nous n’utilisons que Power BI Desktop.
Dans cet article, nous n’allons pas étudier les détails de l’utilisation de Power BI Desktop. Si vous avez besoin d’aide à ce sujet, consultez [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Dans notre exemple, nous utilisons simplement [l’exemple d’analyse des données de vente](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\\power-bi-embedded-iframe\\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Création d’un espace de travail Power BI
Maintenant que l’approvisionnement est fait, nous allons commencer la création de l’espace de travail d’un client dans la collection d’espaces de travail avec les API REST. La requête HTTP POST (REST) suivante crée le nouvel espace de travail dans notre collection d’espaces de travail existante. Dans notre exemple, le nom de la collection d’espaces de travail est **mypbiapp**.
Nous définissons simplement la clé d’accès, que nous avons copiée précédemment, comme **AppKey**. C’est une authentification très simple !

**Demande HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Le **workspaceId** renvoyé est utilisé pour les appels d’API suivants. Notre application doit conserver cette valeur.

## <a name="import-.pbix-file-into-the-workspace"></a>Importer le fichier .pbix dans l’espace de travail
Chaque espace de travail peut héberger un seul fichier Power BI Desktop avec un jeu de données \(y compris les paramètres de source de données) et des rapports. Nous pouvons importer notre fichier .pbix dans l’espace de travail comme le montre le code ci-dessous. Comme vous pouvez le voir, nous pouvons télécharger le binaire du fichier .pbix à l’aide du MIME en plusieurs parties dans HTTP.

Le fragment d’URI **32960a09-6366-4208-a8bb-9e0678cdbb9d** est le workspaceId et le paramètre de requête **datasetDisplayName** est le nom du jeu de données à créer. Le jeu de données créé conserve tous les artefacts liés aux données dans le fichier .pbix, notamment les données importées, le pointeur vers la source de données, etc.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Cette tâche d’importation peut prendre un certain temps. Lorsqu’elle est terminée, notre application peut demander l’état de la tâche avec l’ID d’importation. Dans notre exemple, l’ID d’importation est **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Le code suivant demande l’état avec cet ID d’importation :

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Si la tâche n’est pas terminée, la réponse HTTP peut être la suivante :

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Si la tâche est terminée, la réponse HTTP est plutôt de ce type :

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-\(and-multi-tenancy-of-data)"></a>Connectivité de la source de données \(et architecture mutualisée des données)
Bien que la quasi-totalité des artefacts du fichier .pbix soient importés dans notre espace de travail, les informations d’identification des sources de données ne le sont pas. Par conséquent, lorsque vous utilisez le **mode DirectQuery**, le rapport incorporé ne peut pas s’afficher correctement. Mais lorsque nous utilisons le **mode Import**, nous pouvons afficher le rapport avec les données importées existantes. Dans ce cas, nous devons définir les informations d’identification en procédant comme suit au moyen d’appels REST.

Tout d’abord, nous devons obtenir la source de données de la passerelle. Nous savons que **l’ID** du jeu de données est l’ID renvoyé précédemment.

**Demande HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

À l’aide de l’ID de la passerelle et de l’ID de la source de données renvoyés \(voir les précédents **gatewayId** et **id** dans le résultat renvoyé), nous pouvons modifier les informations d’identification de cette source de données comme suit :

**Demande HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Réponse HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

En production, nous pouvons également définir la chaîne de connexion de chaque espace de travail avec l’API REST. \(autrement dit, nous pouvons séparer la base de données pour chaque client).

La commande suivante change la chaîne de connexion de la source de données avec REST.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Nous pouvons également utiliser la sécurité au niveau de la ligne dans Power BI Embedded et séparer les données de tous les utilisateurs dans un rapport. Par conséquent, nous pouvons attribuer à chaque rapport client le même .pbix \(interface utilisateur, etc.) et différentes sources de données.

> [!NOTE]
> Si vous utilisez le **mode Import** au lieu du **mode DirectQuery**, il est impossible d’actualiser les modèles avec l’API. Et les sources de données locales à travers la passerelle Power BI ne sont pas encore prises en charge dans Power BI Embedded. Toutefois, il est très intéressant de consulter le [blog Power BI](https://powerbi.microsoft.com/blog/) pour connaître les nouveautés et le contenu des futures versions.
> 
> 

## <a name="authentication-and-hosting-(embedding)-reports-in-our-web-page"></a>Authentification et hébergement (incorporation) de rapports dans notre page web
Dans l’API REST précédente, nous pouvons utiliser la clé d’accès **AppKey** elle-même en tant qu’en-tête d’autorisation. Ces appels pouvant être gérés du côté du serveur principal, cette procédure est sécurisée.

Toutefois, lorsque nous incorporons le rapport dans notre page web, ce type d’informations de sécurité serait géré avec JavaScript \(frontal). Dans ce cas, la valeur de l’en-tête d’autorisation doit être sécurisée. Si notre clé d’accès est identifiée par un utilisateur ou du code malveillant, elle peut être utilisée pour appeler toutes les opérations.

Lorsque nous incorporons le rapport dans notre page web, nous devons utiliser le jeton calculé plutôt que la clé d’accès **AppKey**. Notre application doit créer le jeton OAuth Json Web Token \(JWT), qui se compose des revendications et de la signature numérique calculée. Comme illustré ci-dessous, cet OAuth JWT est un ensemble de tokens de chaînes encodés délimités par des points.

![](media\\power-bi-embedded-iframe\\oauth-jwt.png)

Tout d’abord, nous devons préparer la valeur d’entrée, qui est signée ultérieurement. Cette valeur est la chaîne d’URL (rfc4648) encodée en base64 de l’objet JSON suivant. Elles sont délimitées par le caractère point \(.). Plus tard, nous expliquerons comment obtenir l’ID du rapport.

> [!NOTE]
> Si nous souhaitons utiliser la sécurité au niveau des lignes (RLS) avec Power BI Embedded, nous devons également spécifier un **nom d’utilisateur** et des **rôles** dans les revendications.
> 
> 

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Ensuite, nous devons créer la chaîne encodée en base64 du code HMAC \(la signature) avec l’algorithme SHA256. Cette valeur d’entrée signée est la chaîne précédente.

En dernier lieu, nous devons associer la valeur d’entrée et la chaîne de signature avec le point \(.). La chaîne terminée est le jeton d’application pour l’incorporation de rapports. Même si le jeton d’application est identifié par un utilisateur malveillant, celui-ci ne pourra pas obtenir la clé d’accès d’origine. Ce jeton d’application expirera rapidement.

Voici un exemple PHP de ces étapes :

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally,-embed-the-report-into-the-web-page"></a>Dernière étape : intégration du rapport dans la page web
Pour incorporer notre rapport, nous devons récupérer l’URL d’incorporation et **l’ID** de rapport avec l’API REST suivante.

**Demande HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Nous pouvons intégrer le rapport dans notre application web à l’aide du jeton d’application précédent.
Si nous examinons l’exemple de code suivant, la première partie est la même que l’exemple précédent. Dans la dernière partie, cet exemple montre **embedUrl** \(voir le résultat précédent) dans l’iframe et publie le jeton d’application dans l’iframe.

> [!NOTE]
> Vous devez modifier la valeur de l’ID de rapport à votre convenance. En outre, en raison d’un bogue dans notre système de gestion de contenu, la balise iframe dans l’exemple de code est lue littéralement. Supprimez le texte encapsulé de la balise si vous copiez-collez le code suivant.
> 
> 

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Et voici notre résultat :

![](media\\power-bi-embedded-iframe\\view-report.png)

À ce stade, Power BI Embedded affiche uniquement le rapport dans l’iframe. Mais gardez un œil sur le [blog Power BI](). Des améliorations à venir pourraient utiliser de nouvelles API côté client qui nous permettront d’envoyer des informations dans l’iframe ainsi que de récupérer des informations. C’est très intéressant !

## <a name="see-also"></a>Voir aussi
* [Authentification et autorisation dans Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!--HONumber=Oct16_HO2-->


