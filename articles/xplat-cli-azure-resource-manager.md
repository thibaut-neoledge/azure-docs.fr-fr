<properties linkid="script-xplat-intro" urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" title="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaKeywords="windows azure cross-platform command-line interface Resource Manager, windows azure command-line resource manager, azure command-line resource manager, azure cli resource manager" description="Use the Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaCanonical="http://www.windowsazure.com/fr-fr/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/fr-fr/documentation/articles/xplat-cli-azure-resource-manager.md" title="Interface de ligne de commande interplateforme" class="current">Interface de ligne de commande interplateforme</a></div>

Nous avons récemment introduit une version préliminaire du Gestionnaire de ressources comme nouveau moyen de gestion des ressources Azure. Cet article explique comment utiliser l'interface de ligne de commande interplateforme Azure (xplat-cli) pour travailler avec le Gestionnaire de ressources.

> [WACOM.NOTE] Le Gestionnaire de ressources en est actuellement au stade préliminaire et ne fournit pas des capacités de gestion du même niveau qu'Azure Service Management.

> [WACOM.NOTE] Si vous n'avez pas encore installé et configuré xplat-cli, consultez la rubrique [Installation et configuration de l'interface de ligne de commande interplateforme Microsoft Azure][Installation et configuration de l'interface de ligne de commande interplateforme Microsoft Azure] pour connaître les procédures d'installation, de configuration et d'utilisation de xplat-cli.

## Gestionnaire de ressources

Le Gestionnaire de ressources permet de prendre en charge la gestion d'un groupe de *ressources* (entités gérées par l'utilisateur, telles qu'un serveur de bases de données, une base de données ou un site web) comme une seule unité logique ou *groupe de ressources*. Par exemple, un groupe de ressources peut contenir des ressources de site web et de base de données SQL.

Pour prendre en charge une méthode plus déclarative de description des changements des ressources d'un groupe de ressources, le Gestionnaire de ressources utilise des *modèles*, qui sont des documents JSON. Le langage des modèles permet également de décrire les paramètres qui peuvent être renseignés en ligne lors de l'exécution d'une commande ou stockés dans un fichier JSON séparé. Vous pouvez donc facilement créer de nouvelles ressources en utilisant le même modèle et en changeant simplement les paramètres. Par exemple, un modèle qui crée un site web a des paramètres pour le nom du site, la région du site web et d'autres paramètres courants.

> [WACOM.NOTE] Les spécificités du langage des modèles ne sont pas documentées ici. Lorsque la documentation sera disponible, cette rubrique sera mise à jour avec un lien vers la documentation de référence.
>
> Vous pouvez toutefois utiliser la commande `azure group template download` pour télécharger et modifier les modèles fournis par Microsoft et ses partenaires dans la galerie des modèles.

Lorsqu'un modèle est utilisé pour modifier ou créer un groupe, un *déploiement* est créé, avant d'être appliqué au groupe.

## Authentification

Actuellement, pour utiliser le Gestionnaire de ressources avec l'interface xplat-cli, vous devez vous authentifier sur Microsoft Azure en utilisant un compte professionnel. L'authentification avec un compte Microsoft ou un certificat installé avec un fichier .publishsettings ne fonctionne pas.

Pour plus d'informations sur l'authentification en utilisant un compte professionnel, consultez la page [Installation et configuration de l'interface de ligne de commande interplateforme Azure][Installation et configuration de l'interface de ligne de commande interplateforme Microsoft Azure].

## Utilisation de groupes et de modèles

1.  Le Gestionnaire de ressources est au stade de version préliminaire, ce qui explique pourquoi les commandes xplat-cli associées ne sont pas activées par défaut. Pour activer les commandes, utilisez la commande suivante :

        azure config mode arm

    > [WACOM.NOTE] Le mode Gestionnaire de ressources et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

2.  Lorsque vous utilisez des modèles, vous pouvez créer vos modèles ou utiliser ceux de la galerie. Pour répertorier les modèles disponibles dans la galerie, utilisez la commande suivante.

        azure group template list

    La réponse contient le nom de l'éditeur et celui du modèle, comme suit.

        data:    Publisher               Name
        data:    ----------------------------------------------------------------------------
        data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
        data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
        data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
        data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
        data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3.  Pour afficher les détails d'un modèle qui va créer un site web Azure, utilisez la commande suivante.

        azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    Des informations sur le modèle sont alors renvoyées.

4.  Lorsque vous avez sélectionné un modèle, vous pouvez le télécharger avec la commande suivante.

        azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    Les modèles téléchargés peuvent être personnalisés pour vos besoins. Vous pouvez, par exemple, ajouter une ressource.

    > [WACOM.NOTE] Si vous modifiez le modèle, utilisez la commande `azure group template validate` pour le valider avant de l'utiliser pour créer ou modifier un groupe de ressources.

5.  Ouvrez le fichier de modèle dans un éditeur de texte. Notez la collection **parameters** vers le début. Elle contient la liste des paramètres attendus par ce modèle pour créer les ressources décrites par le modèle. Certains paramètres, comme **sku**, ont des valeurs par défaut, alors que d'autres spécifient simplement le type de la valeur, comme **siteName**. Lorsque vous utilisez un modèle, vous pouvez fournir des paramètres de ligne de commande ou spécifier un fichier contenant les valeurs des paramètres. Dans les deux cas, les paramètres doivent être au format JSON.

    Pour créer un fichier qui contient les paramètres du modèle Microsoft.WebSiteSQLDatabase.0.1.0-preview1, utilisez les données suivantes et créez un fichier nommé **params.json**. Remplacez les valeurs commençant par **My**, comme **MyWebSite**, par vos propres valeurs. **siteLocation** doit spécifier une région Azure proche de chez vous, comme **North Europe** ou **South Central US**.

        {
          "siteName": {
            "value": "MyWebSite"
          },
          "hostingPlanName": {
            "value": "MyHostingPlan"
          },
          "siteLocation": {
            "value": "North Europe"
          },
          "serverName": {
            "value": "MySQLServer"
          },
          "serverLocation": {
            "value": "North Europe"
          },
          "administratorLogin": {
            "value": "MySQLAdmin"
          },
          "administratorLoginPassword": {
            "value": "MySQLAdminPassword"
          },
          "databaseName": {
            "value": "MySQLDB"
          }
        }

6.  Après avoir enregistré le fichier **params.json**, utilisez la commande suivante pour créer un groupe de ressources basé sur le modèle. Le paramètre `-e` spécifie le fichier **params.json** créé à l'étape précédente.

        azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

    Remplacez **MyGroupName** par le nom du groupe que vous souhaitez utiliser et **MyDataCenter** par la valeur **siteLocation** spécifiée dans le modèle.

    > [WACOM.NOTE] Cette commande renvoie la valeur OK une fois le déploiement chargé, mais avant qu'il soit appliqué aux ressources du groupe. Pour vérifier le statut du déploiement, utilisez la commande suivante.
    >
    > `azure group deployment show MyGroupName MyDeployment`
    >
    > **ProvisioningState** indique le statut du déploiement.
    >
    > Si vous réalisez que votre configuration n'est pas correcte et que vous devez arrêter un déploiement long, utilisez la commande suivante.
    >
    > `azure group deployment stop MyGroupName MyDeployment`
    >
    > Si vous n'indiquez pas de nom de déploiement, un nom est créé automatiquement d'après celui du fichier de modèle. Il est renvoyé dans la sortie de la commande `azure group create`.

7.  Pour afficher le groupe, utilisez la commande suivante.

        azure group show MyGroupName

    Cette commande renvoie des informations sur les ressources du groupe. Si vous avez plusieurs groupes, vous pouvez utiliser la commande `azure group list` pour extraire la liste des noms de groupes et `azure group show` pour afficher les détails d'un groupe donné.

## Utilisation des ressources

Les modèles vous permettent de déclarer des modifications de la configuration au niveau du groupe, mais vous devez parfois vous concentrer sur une ressource spécifique. Pour ce faire, utilisez les commandes `azure resource`.

> [WACOM.NOTE] Lorsque vous utilisez des commandes `azure resource` autres que la commande `list`, vous devez spécifier la version de l'API de la ressource avec laquelle vous travaillez au moyen du paramètre `-o`. Si vous ne connaissez pas la version de l'API à utiliser, consultez le fichier de modèle et recherchez le champ **apiVersion** de la ressource.

1.  Pour répertorier toutes les ressources d'un groupe, utilisez la commande suivante.

        azure resource list MyGroupName

2.  Pour afficher des ressources individuelles, comme le site web, dans le groupe, utilisez la commande suivante.

        azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

    Notez le paramètre **Microsoft.Web/sites**. Il indique le type de la ressource sur laquelle vous demandez des informations. Si vous analysez le fichier de modèle téléchargé plus tôt, vous pouvez noter que cette même valeur est utilisée pour définir le type de la ressource Site web décrite dans le modèle.

    Cette commande renvoie des informations concernant le site web. Par exemple, le champ **hostNames** doit contenir l'URL du site web. Utilisez-la dans votre navigateur pour vérifier si le site web fonctionne.

3.  Lorsque vous affichez des détails sur une ressource, nous vous recommandons d'utiliser le paramètre `--json`, qui facilite la lecture de la sortie, car certaines valeurs sont des structures imbriquées ou des collections. Le code suivant présente le retour des résultats de la commande show dans un document JSON.

        azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

    > [WACOM.NOTE] Vous pouvez enregistrer les données JSON dans un fichier au moyen du caractère \> pour canaliser la sortie vers le fichier. Par exemple :
    >
    > `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

4.  Pour supprimer une ressource, utilisez la commande suivante.

        azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

## Journalisation

Pour afficher les informations de journalisation sur les opérations effectuées sur un groupe, utilisez la commande `azure group log show`. Par défaut, la dernière opération effectuée sur le groupe sera répertoriée. Pour afficher toutes les opérations, utilisez le paramètre facultatif `--all`. Pour le dernier déploiement, utilisez `--last-deployment`. Pour un déploiement spécifique, utilisez `--deployment`, puis spécifiez le nom du déploiement. L'exemple suivant renvoie toutes les opérations effectuées sur le groupe « MyGroup ».

    azure group log show mygroup --all

## Étapes suivantes

-   Pour plus d'informations sur l'utilisation de l'interface de ligne de commande interplateforme Azure, consultez la rubrique [Installation et configuration de l'interface de ligne de commande interplateforme Azure][Installation et configuration de l'interface de ligne de commande interplateforme Microsoft Azure].
-   Pour plus d'informations sur l'utilisation du Gestionnaire de ressources avec Windows Azure PowerShell, consultez la rubrique [Prise en main de l'utilisation de Windows PowerShell avec le Gestionnaire de ressources][Prise en main de l'utilisation de Windows PowerShell avec le Gestionnaire de ressources].

  [Installation et configuration de l'interface de ligne de commande interplateforme Microsoft Azure]: /fr-fr/documentation/articles/xplat-cli/
  [Prise en main de l'utilisation de Windows PowerShell avec le Gestionnaire de ressources]: http://go.microsoft.com/fwlink/?LinkId=394760
