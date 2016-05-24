<properties
   pageTitle="Version préliminaire de Microsoft Power BI Embedded - Incorporer un rapport Power BI avec un IFrame"
   description="Version préliminaire de Microsoft Power BI Embedded - Code de base pour intégrer un rapport dans votre application, comment s’authentifier avec le jeton d’application Power BI Embedded, comment obtenir des rapports"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# Incorporer un rapport Power BI avec un IFrame
Cet article vous présente un code essentiel à l’utilisation de l’API REST **Power BI Embedded**, des jetons d’application, un IFrame et du code JavaScript pour intégrer ou incorporer un rapport dans votre application.

Dans [Prise en main de la version préliminaire de Microsoft Power BI Embedded](power-bi-embedded-get-started.md), vous apprendrez à configurer une **collection d’espaces de travail** pour contenir un ou plusieurs **espaces de travail** pour le contenu de votre rapport. Ensuite, dans [Prise en main de l’exemple Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md), vous importerez un rapport dans un **espace de travail**.

Cet article vous montre comment incorporer un rapport dans votre application. Pour suivre la procédure dans cet article, vous devrez télécharger l’exemple d’[intégration de rapport avec un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) sur GitHub. Cet exemple est une application de formulaire web ASP.NET simple, destinée à illustrer des codes C# et JavaScript de base dont vous aurez besoin pour intégrer un rapport. Pour obtenir un exemple plus avancé qui utilise le modèle de conception Model-View-Controller (MVC) pour intégrer un rapport, consultez l’[exemple d’application web de tableau de bord](http://go.microsoft.com/fwlink/?LinkId=761493) sur GitHub.

Les descriptions pour intégrer un rapport **Power BI Embedded** dans votre application se trouvent ci-dessous.

Voici les étapes permettant d’intégrer un rapport.

- Étape 1 : [Obtenir un rapport dans un espace de travail](#GetReport). Dans cette étape, vous utilisez un flux de jeton d’application pour obtenir un jeton d’accès afin d’appeler l’opération REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx). Une fois un rapport obtenu à partir de la liste **Get Reports**, vous incorporez le rapport dans une application avec un élément **IFrame**.
- Étape 2 : [Incorporer un rapport dans une application](#EmbedReport). Dans cette étape, vous utiliserez un jeton d’incorporation pour un rapport, du code JavaScript et un IFrame pour intégrer ou incorporer un rapport dans une application web.

Si vous souhaitez exécuter l’exemple pour savoir comment intégrer un rapport, téléchargez l’exemple d’[intégration de rapport avec un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) sur GitHub, puis configurez les trois paramètres Web.Config :

- **AccessKey**: un **AccessKey** est utilisé pour générer un jeton web JSON (JWT), servant à obtenir et à incorporer des rapports. Pour découvrir comment obtenir un **AccessKey**, consultez [Prise en main de la version préliminaire de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceName** : pour découvrir comment obtenir un **WorkspaceName**, consultez [Prise en main de la version préliminaire de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceId** : pour découvrir comment obtenir un **WorkspaceId**, consultez [Prise en main de la version préliminaire de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

Les sections suivantes vous présentent le code nécessaire pour intégrer un rapport.

<a name="GetReport"/>
## Obtenir un rapport dans un espace de travail

Pour intégrer un rapport dans une application, vous aurez besoin d’un **ID** et d’un **embedUrl** de rapport. Pour obtenir un **ID** et **embedUrl** de rapport, vous devez appeler l’opération REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx), puis choisir un rapport dans la liste JSON. Dans [Incorporer un rapport dans une application](#EmbedReport), vous utilisez un **ID** et **embedUrl** de rapport pour incorporer le rapport dans votre application.

### Réponse JSON de Get Reports
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Pour appeler l’opération REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx), vous utilisez un jeton d’application. Pour plus d’informations sur le flux de jeton d’application, consultez [À propos du flux de jetons d’application dans Power BI Embedded](power-bi-embedded-app-token-flow.md). Le code suivant décrit comment obtenir une liste JSON de rapports. Pour incorporer un rapport, consultez [Incorporer un rapport dans une application](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Incorporer un rapport dans une application

Avant d’incorporer un rapport dans votre application, vous aurez besoin d’un jeton d’incorporation pour un rapport. Ce jeton ressemble à un jeton d’application utilisé pour appeler les opérations REST **Power BI Embedded**, mais est généré pour une ressource de rapport plutôt que pour une ressource REST. Voici le code pour obtenir un jeton d’application pour un rapport. Pour utiliser le jeton d’application pour un rapport, consultez [Incorporer un rapport dans votre application](#EmbedReportJS).

<a name="EmbedReportToken"/>
### Obtenir un jeton d’application pour un rapport

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Incorporer un rapport dans votre application

Pour incorporer un rapport **Power BI** dans votre application, vous utilisez un IFrame et du code JavaScript. Voici un exemple d’IFrame et de code JavaScript pour incorporer un rapport. Pour afficher tous les exemples de code pour incorporer un rapport, consultez l’exemple d’[intégration de rapport avec un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) sur GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Une fois qu’un rapport est incorporé dans votre application, vous pouvez filtrer ce dernier. La section suivante vous montre comment filtrer un rapport à l’aide d’une syntaxe d’URL.

## Filtrer un rapport

Vous pouvez filtrer un rapport incorporé à l’aide d’une syntaxe d’URL. Pour ce faire, ajoutez un paramètre de chaîne de requête à l’URL SCRL iFrame avec le filtre spécifié. Vous pouvez **filtrer sur une valeur** et **masquer le volet Filtre**.


**Filtrer sur une valeur**

Pour filtrer sur une valeur, vous utilisez la syntaxe de requête **$filter** avec un opérateur **eq** comme suit :

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Par exemple, vous pouvez filtrer la valeur Store Chain (chaîne de magasin) sur « Lindseys ». La partie Filtre de l’URL ressemblerait à ceci :

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} ne peut pas contenir d’espaces ou de caractères spéciaux. {fieldValue} accepte une seule valeur de catégorie.

**Masquer le volet Filtre**

Pour masquer le **volet Filtre**, ajoutez **filterPaneEnabled** à la chaîne de requête d’état comme suit :

```
&filterPaneEnabled=false
```

## Conclusion

Cet article vous a présenté le code nécessaire à l’intégration d’un rapport **Power BI** dans votre application. Pour rapidement commencer l’intégration d’un rapport dans une application, téléchargez ces exemples sur GitHub :

- [Exemple d’intégration d’un rapport avec un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Exemple d’application web de tableau de bord](http://go.microsoft.com/fwlink/?LinkId=761493)

## Voir aussi
- [Prise en main de la version préliminaire de Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Prise en main de l’exemple](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx)

<!---HONumber=AcomDC_0518_2016-->