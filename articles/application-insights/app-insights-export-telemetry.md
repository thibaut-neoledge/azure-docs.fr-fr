<properties 
	pageTitle="Exportation continue de télémétrie depuis Application Insights" 
	description="Exportez les données de diagnostic et les données d’utilisation dans le stockage Microsoft Azure et téléchargez-les à partir de là." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 
# Exporter la télémétrie depuis Application Insights

Vous souhaitez effectuer une analyse personnalisée de votre télémétrie ? Ou peut-être voulez-vous recevoir une alerte par courrier électronique pour les événements présentant des propriétés spécifiques ? L’exportation continue est idéale dans ce cas. Les événements que vous voyez dans le portail Application Insights peuvent être exportés vers le stockage Microsoft Azure au format JSON. À partir de là, vous pouvez télécharger vos données et écrire le code pour pouvoir les traiter.


## <a name="setup"></a> Configurer l’exportation continue

Dans le panneau Vue d’ensemble de votre application dans le portail Application Insights, ouvrez Exportation continue :

![Faites défiler vers le bas, puis cliquez sur Exportation continue.](./media/app-insights-export-telemetry/01-export.png)

Ajoutez une exportation, puis choisissez le [compte de stockage Azure](../storage-introduction.md) dans lequel vous souhaitez placer les données :

![Cliquez sur Ajouter, Destination de l’exportation, Compte de stockage, puis créez un nouveau magasin ou choisissez un magasin existant.](./media/app-insights-export-telemetry/02-add.png)

Choisissez les types d’événement que vous souhaitez exporter :

![Cliquez sur Choisir les types d’événements.](./media/app-insights-export-telemetry/03-types.png)


Une fois que vous avez créé l’exportation, elle démarre. (Vous n’obtenez que les données qui arrivent après la création de l’exportation.)


Si vous souhaitez modifier les types d’événement plus tard, modifiez simplement l’exportation :

![Cliquez sur Choisir les types d’événements.](./media/app-insights-export-telemetry/05-edit.png)

Pour arrêter le flux de données, cliquez sur Désactiver. Lorsque vous cliquez de nouveau sur Activer, le flux de données redémarre avec de nouvelles données. Vous n’obtiendrez pas les données qui sont arrivées sur le portail alors que l’exportation était désactivée.

Pour arrêter définitivement le flux de données, supprimez l’exportation. Cette opération ne supprime pas vos données du stockage.
#### Impossible d’ajouter ou de modifier une exportation ?

* Pour ajouter ou modifier des exportations, vous devez disposer de droits d’accès de propriétaire, de collaborateur ou de collaborateur Application Insights. [En savoir plus sur les rôles][roles].

## <a name="analyze"></a> Quels sont les événements que vous obtenez ?

Les données exportées sont celles de la télémétrie brute que nous recevons de votre application, à quelques exceptions près :

* Les résultats des tests web ne sont pas actuellement inclus. 
* Nous ajoutons les données d’emplacement que nous calculons à partir de l’adresse IP du client.  

Les mesures calculées ne sont pas incluses. Par exemple, nous n’exportons pas l’utilisation moyenne du processeur, mais nous exportons la télémétrie brute à partir de laquelle la moyenne est calculée.

## <a name="get"></a> Comment les obtenir ?

Lorsque vous ouvrez votre magasin d’objets blob avec un outil comme l’[Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx), vous voyez un conteneur avec un ensemble de fichiers blob. L’URI de chaque fichier est id-application/type-télémétrie/date/heure.

![Inspectez le magasin d’objets blob avec un outil adapté.](./media/app-insights-export-telemetry/04-data.png)

La date et l’heure sont au format UTC et correspondent au moment où la télémétrie a été placée dans le magasin, et pas au moment où elle a été générée. Par conséquent, si vous écrivez du code pour télécharger les données, il peut parcourir les données de façon linéaire.

Pour télécharger ces données par programme, utilisez l’[API REST du magasin d’objets blob](../storage-dotnet-how-to-use-blobs.md#configure-access) ou les [applets de commande PowerShell Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx).

Ou pensez à [DataFactory](http://azure.microsoft.com/services/data-factory/), qui permet de définir des pipelines pour gérer des données à grande échelle.

Nous commençons l’écriture d’un nouvel objet blob toutes les heures (en cas de réception d’événements). Par conséquent, vous devez toujours traiter jusqu’à l’heure précédente, mais attendre que l’heure en cours se termine.

[Exemple de code][exportcode]


## <a name="format"></a> À quoi ressemblent les données ?

* Chaque objet blob est un fichier texte qui contient plusieurs lignes séparées par des \\n.
* Chaque ligne est un document JSON sans mise en forme. Pour consulter ce format, vous pouvez l’ouvrir par exemple dans Notepad++ avec le plug-in JSON :

![Consultez la télémétrie avec un outil approprié.](./media/app-insights-export-telemetry/06-json.png)

Les durées sont exprimées en nombre de cycles, où 10 000 cycles = 1 ms. Par exemple, ces valeurs indiquent une durée de 10 ms pour envoyer une demande à partir du navigateur, 30 ms pour la recevoir et 1,8 s pour traiter la page dans le navigateur :

	"sendRequest": {"value": 10000.0},
	"receiveRequest": {"value": 30000.0},
	"clientProcess": {"value": 17970000.0}



## Comment les traiter ?

À petite échelle, vous pouvez écrire du code pour décomposer vos données, les lire dans une feuille de calcul et ainsi de suite. Par exemple :

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }


Ou vous pouvez les déplacer dans une base de données SQL. Consultez cet [exemple de code][exportcode].

À plus grande échelle, envisagez d’utiliser des clusters [HDInsight](http://azure.microsoft.com/services/hdinsight/) - Hadoop dans le cloud. HDInsight offre de nombreuses technologies pour gérer et analyser les données volumineuses.

## <a name="delete"></a>Supprimer les anciennes données
Notez que vous êtes responsable de la gestion de votre capacité de stockage et de la suppression des anciennes données si nécessaire.

## Si vous régénérez votre clé de stockage...

Si vous modifiez la clé de votre stockage, l’exportation continue cesse de fonctionner. Vous voyez alors une notification dans votre compte Azure.

Ouvrez le panneau Exportation continue et modifiez votre exportation. Modifiez la destination de l’exportation, mais laissez le même stockage sélectionné. Cliquez sur OK pour confirmer.

![Modifiez l’exportation continue, ouvrez puis fermez la destination de l’exportation.](./media/app-insights-export-telemetry/07-resetstore.png)

L’exportation continue redémarre.


## Exemple de code

[Déplacer les données exportées dans une base de données SQL][exportcode]

## Questions et réponses

* *Je veux simplement télécharger un graphique.*  
 
    Nous travaillons sur cette option séparément.

* *J’ai configuré une exportation, mais il n’y a pas de données dans mon magasin.*

    Application Insights a-t-il reçu de la télémétrie de votre application depuis que vous avez configuré l’exportation ? Vous recevrez uniquement les nouvelles données.

* *J’ai essayé de configurer une exportation, mais l’accès lui a été refusé.*

    Si le compte appartient à votre organisation, vous devez être membre du groupe des propriétaires ou des collaborateurs.

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *Puis-je exporter directement vers mon propre magasin local ?*

    Non. Notre moteur d’exportation doit pouvoir compter sur un haut débit pour transmettre les données.

* *Existe-t-il une limite à la quantité de données qu’il est possible de placer dans mon magasin ?*

    Non. Nous transmettons les données jusqu’à ce que vous supprimiez l’exportation. Nous arrêtons si nous atteignons les limites extérieures du stockage d’objets blob, mais ceci représente un volume très important. C’est à vous de contrôler la quantité de stockage vous utilisez.

* *J’ai régénéré la clé de mon espace de stockage ou modifié le nom du conteneur et l’exportation ne fonctionne plus.*

    Modifiez l’exportation et ouvrez le panneau de destination d’exportation. Conservez le même stockage que celui sélectionné auparavant, puis cliquez sur OK pour confirmer. L’exportation redémarre. Si la modification a eu lieu dans les derniers jours, vous ne perdrez pas de données.

* *Est-il possible de suspendre l’exportation ?*

    Oui. Cliquez sur Désactiver.


<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[roles]: app-insights-resources-roles-access-control.md


<!--HONumber=54--> 