<properties
   pageTitle="Prise en main d’un exemple"
   description="Power BI Embedded, utiliser SDK pour ajouter des rapports interactifs Power BI à votre application business intelligence"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/14/2016"
   ms.author="mblythe"/>

# Prise en main de l’exemple Power BI Embedded

Avec **Microsoft Power BI Embedded**, vous pouvez intégrer des rapports Power BI dans vos applications web ou mobiles. Dans cet article, nous vous présenterons l’exemple de prise en main **Power BI Embedded**.

Avant de poursuivre, vous souhaiterez probablement enregistrer les ressources suivantes. Elles vous aideront lors de l’intégration de rapports Power BI dans l’exemple d’application et vos propres applications également.

 -	[Exemple d’application web de tableau de bord](http://go.microsoft.com/fwlink/?LinkId=761493)
 -	[Référence de l’API Power BI Embedded](https://msdn.microsoft.com/library/mt711493.aspx)
 -	[Kit de développement logiciel (SDK) .NET Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=746472) (disponible par le biais de NuGet)



> [AZURE.NOTE] Avant de configurer et d’exécuter l’exemple de prise en main de Power BI Embedded, vous devez créer au moins une **collection d’espaces de travail** dans votre abonnement Azure. Pour savoir comment créer une **collection d’espaces de travail** dans le portail Azure, consultez [Prise en main de Power BI Embedded (aperçu)](power-bi-embedded-get-started.md).

## Configurer l’exemple d’application

Passons à la configuration de votre environnement de développement Visual Studio pour accéder aux composants nécessaires à l’exécution de l’exemple d’application.

1. Téléchargez et décompressez l’exemple [Power BI Embedded - Intégrer un rapport dans une application web](http://go.microsoft.com/fwlink/?LinkId=761493) sur GitHub.

2. Ouvrez **PowerBI-embedded.sln** dans Visual Studio. Vous devrez peut-être exécuter la commande **Update-Package** dans la Console du Gestionnaire de Package NuGet pour mettre à jour les packages utilisés dans cette solution.

3. Générez la solution.

4. Exécutez l’application de console **ProvisionSample**. Dans l’exemple d’application console, vous allez approvisionner un espace de travail et importer un fichier PBIX.

5. Pour approvisionner un nouvel **espace de travail**, sélectionnez l’option 5, **Approvisionner un nouvel espace de travail dans une collection d’espaces de travail existante**.

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. Entrez le nom de votre **collection d’espaces de travail** et la **clé d’accès**. Vous pouvez les obtenir dans le **Portail Azure**. Pour en savoir plus sur la façon d’obtenir votre **clé d’accès**, consultez [Affichage des touches d’accès rapide aux API de Power BI](power-bi-embedded-get-started-sample.md#view-access-keys) dans Prise en main de Microsoft Power BI Embedded.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. Copiez et enregistrez **l’ID d’espace de travail** qui vient d’être créé et qui sera utilisé ultérieurement dans cet article. Une fois **l’ID d’espace de travail** créé, il est disponible dans le **Portail Azure**.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. Pour importer un fichier PBIX dans votre **espace de travail**, sélectionnez l’option **6. Importez le fichier PBIX Desktop dans un espace de travail existant**. Si vous n’avez pas de fichier PBIX sous la main, téléchargez [l’exemple PBIX Analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547).

9. Si vous y êtes invité, entrez un nom convivial pour votre **jeu de données**.

La réponse doit ressembler à ceci :

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] Si votre fichier PBIX contient des connexions de requête directe, exécutez l’option 7 pour mettre à jour les chaînes de connexion.

À ce stade, vous avez un rapport PBIX Power BI qui a été importé dans votre **espace de travail**. Voyons maintenant comment exécuter l’exemple d’application web de prise en main de **Power BI Embedded**.

## Exécuter l’exemple d’application web

L’exemple d’application web est un tableau de bord qui restitue les rapports importés dans votre **espace de travail**. Voici comment configurer l’exemple d’application web.

1. Dans la solution Visual Studio **PowerBI-embedded**, cliquez avec le bouton droit sur l’application web **EmbedSample**, puis choisissez **Définir comme projet de démarrage**.
2. Dans **web.config**, dans l’application web **EmbedSample**, modifiez la section **appSettings** : **AccessKey**, le nom **WorkspaceCollection** et **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Exécutez l’application web **EmbedSample**.

Une fois que vous avez exécuté l’application web **EmbedSample**, le volet de navigation gauche doit contenir un menu **Rapports**. Pour afficher le rapport que vous avez importé, développez **Rapports**, puis cliquez sur un rapport. Si vous avez importé l’[exemple PBIX Analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547), l’exemple d’application web a l’aspect suivant :

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

Une fois que vous avez cliqué sur un rapport, l’application web **EmbedSample** doit avoir l’aspect suivant :

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)


## Explorer l’exemple de code
L’exemple **Microsoft Power BI Embedded** est un exemple de tableau de bord d’application web qui vous montre comment intégrer des rapports **Power BI** dans votre application. Il utilise un modèle de conception MVC (Model-View-Controller) pour illustrer les meilleures pratiques. Cette section met en évidence les parties de l’exemple de code que vous pouvez explorer dans la solution d’application web **PowerBI-embedded**. Le modèle MVC (Model-View-Controller) sépare la modélisation du domaine, la présentation et les actions basées sur les entrées des utilisateurs en trois classes distinctes : modèle, affichage et contrôle. Pour plus d’informations sur MVC, consultez [En savoir plus sur ASP.NET](http://www.asp.net/mvc).

L’exemple **Microsoft Power BI Embedded** inclut les sections suivantes. Chacune d’elles inclut le nom de fichier dans la solution PowerBI-embedded.sln afin que vous puissiez facilement trouver le code dans l’exemple.

> [AZURE.NOTE] Cette section est un résumé de l’exemple de code qui montre comment le code a été écrit. Pour afficher l’exemple complet, chargez la solution PowerBI-embedded.sln dans Visual Studio.

### Modèle
L’exemple inclut deux modèles : **ReportsViewModel** et **ReportViewModel**.

**ReportsViewModel.cs** : représente les rapports Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs** : représente un rapport Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Chaîne de connexion
La chaîne de connexion doit avoir le format suivant :

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

L’utilisation d’attributs de serveur et de base de données communs échoue. Par exemple : Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### Affichage
**L’affichage** gère la présentation des **rapports** Power BI et d’un **rapport** Power BI.

**Reports.cshtml** : effectue une itération sur **Model.Reports** pour créer un **ActionLink**. **ActionLink** est composé comme suit :

|Partie|Description
|---|---
|Intitulé| Nom du rapport.
|QueryString| Lien vers l’ID de rapport.

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml : définit **Model.AccessToken** et l’expression lambda pour **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs** : crée un PowerBIClient qui transmet un **jeton d’application**. Un jeton JWT (JSON Web Token) est généré à partir de la **clé de signature** pour obtenir les **informations d’identification**. Les **informations d’identification** sont utilisées pour créer une instance de **PowerBIClient**. Une fois que vous avez une instance de **PowerBIClient**, vous pouvez appeler GetReports() et GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Intégrer un rapport dans votre application

Une fois que vous avez un **rapport**, utilisez un **iframe** pour incorporer le **rapport** Power BI. Voici un extrait de code powerbi.js dans l’exemple **Microsoft Power BI Embedded**.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


## Filtrer les rapports incorporés dans votre application

Vous pouvez filtrer un rapport incorporé à l’aide d’une syntaxe d’URL. Pour ce faire, ajoutez un paramètre de chaîne de requête **$filter** avec un opérateur **eq** à l’URL src iframe avec le filtre spécifié. Voici la syntaxe de requête de filtre :

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} ne peut pas contenir d’espaces ou de caractères spéciaux. {fieldValue} accepte une seule valeur de catégorie.


## Voir aussi

- [Scénarios Microsoft Power BI Embedded courants](power-bi-embedded-scenarios.md)
- [Authentification et autorisation dans Power BI Embedded](power-bi-embedded-app-token-flow.md)

<!---HONumber=AcomDC_0907_2016-->