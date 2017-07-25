---
title: "Développer et exécuter des fonctions Azure en local | Microsoft Docs"
description: "Apprenez à coder et à tester des fonctions Azure sur votre ordinateur local avant de les exécuter dans Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: fr-fr
ms.lasthandoff: 06/09/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Coder et tester des fonctions Azure localement

Vous pouvez utiliser votre éditeur de code favori et les outils de développement local pour exécuter le runtime Azure Functions localement. Réagissez aux événements dans Azure et déboguez les fonctions JavaScript et C#.

Pour commencer, installez [Azure Functions Core Tools] à partir de npm. Azure Functions Core Tools est une version locale du runtime Azure Functions que vous pouvez exécuter sur votre ordinateur Windows local. Ce n’est pas un émulateur ni un simulateur. Il s’agit du même runtime que celui qui s’exécute dans Azure.

[Azure Functions Core Tools] ajoute les alias de commande suivants :
- `func`
- `azfun`
- `azurefunctions`

Azure Functions Core Tools est [disponible en open source et hébergé sur GitHub](https://github.com/azure/azure-functions-cli). Pour enregistrer un bogue ou une demande de fonctionnalité, [créez un problème GitHub](https://github.com/azure/azure-functions-cli/issues).

## <a name="create-a-local-functions-project"></a>Créer un projet Functions local

Quand il est exécuté localement, un projet Functions est un répertoire qui contient les fichiers host.json et local.settings.json. Ce répertoire est l’équivalent d’une application de fonction dans Azure. Pour plus d’informations sur la structure de dossiers Azure Functions, consultez le [Guide de développement Azure Functions](functions-reference.md#folder-structure).

Depuis une invite de commandes, exécutez la commande suivante :

```
func init MyFunctionProj
```

La sortie ressemble à l’exemple suivant :

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Pour ne pas créer de dépôt Git, utilisez l’option `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Fichier de paramètres locaux

Le fichier local.settings.json stocke les paramètres de l’application, les chaînes de connexion et les paramètres d’Azure Functions Core Tools. Sa structure est la suivante :

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

Spécifiez les paramètres d’application dans la collection **Values**. Ces paramètres peuvent ensuite être lus en tant que variables d’environnement. En C#, utilisez `System.Environment.GetEnvironmentVariable` ou `ConfigurationManager.AppSettings`. En JavaScript, utilisez `process.env`. Si le même paramètre est spécifié comme variable d’environnement système, il est prioritaire sur les valeurs contenues dans local.settings.json.

Le paramètre d’application **AzureWebJobsStorage** est un paramètre spécial exigé par le runtime Azure Functions pour tous les déclencheurs autres que HTTP. En interne, le runtime crée les files d’attente pour gérer les déclencheurs dans ce compte de stockage. Si aucune valeur n’est spécifiée pour **AzureWebJobsStorage** et que vous utilisez des déclencheurs autres que HTTP, le message suivant apparaît :

*Valeur manquante pour AzureWebJobsStorage dans local.settings.json. Cette valeur est nécessaire pour tous les déclencheurs autres que HTTP. Vous pouvez exécuter 'func azure functionapp fetch-app-settings <functionAppName>' ou spécifier une chaîne de connexion dans local.settings.json.*

> [!NOTE]
> Il est possible d’utiliser l’émulateur de stockage local, par le biais de la chaîne de connexion "AzureWebJobsStorage": "UseDevelopmentStorage=true". Toutefois, il peut y avoir des différences de comportement par rapport au service de stockage Azure.

Les paramètres suivants personnalisent l’hôte Functions local :
- `LocalHttpPort`. Port par défaut à utiliser pour `func host start` `func run`. L’option de ligne de commande `--port` est prioritaire sur cette valeur.
- `CORS`. Origines CORS autorisées, sous la forme d’une liste séparée par des virgules, sans espaces. Utilisez « * » pour les autoriser toutes.

Vous pouvez fournir des chaînes de connexion dans l’objet `ConnectionStrings`. Elles sont ajoutées dans l’environnement avec le nom du fournisseur **System.Data.SqlClient**.

La plupart des déclencheurs et des liaisons ont une propriété **Connection** qui correspond au nom d’une variable d’environnement ou d’un paramètre d’application dans local.settings.json. Si la valeur du paramètre d’application est manquante, le message suivant apparaît :

*Avertissement : local.settings.json ne contient aucune valeur 'MyStorageConnection' correspondant à la propriété 'connection' sur 'blobTrigger' dans 'BlobTriggerCSharp\function.json'. Vous pouvez exécuter 'func azure functionapp fetch-app-settings <functionAppName>' ou spécifier une chaîne de connexion dans local.settings.json.*

Le fichier local.settings.json n’est utilisé que par Azure Functions Core Tools. Pour définir des paramètres d’application et des chaînes de connexion dans Azure, utilisez le panneau **Paramètres de l’application**.

### <a name="configure-app-settings"></a>Configuration des paramètres d’application

Pour définir une valeur pour des chaînes de connexion, vous pouvez effectuer une des opérations suivantes :
- Entrez manuellement une chaîne de connexion à partir de [l’Explorateur de stockage Azure](http://storageexplorer.com/).
- Utilisez **func azure functionapp fetch-app-settings \<nom_application_de_fonction\>**. Nécessite une **connexion azure**.
- Utilisez **func azure functionapp storage fetch-connection-string \<nom_compte_de_stockage\>**. Nécessite une **connexion azure**.

## <a name="create-a-function"></a>Créer une fonction

Pour créer une fonction, exécutez `func new`. Cette commande dispose des arguments facultatifs suivants :

- `--language [-l]`. Langage de programmation du modèle, tel que C#, F# ou JavaScript.
- `--template [-t]`. Nom du modèle.
- `--name [-n]`. Nom de la fonction.

Par exemple, pour créer un déclencheur HTTP JavaScript, exécutez :

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Pour créer une fonction déclenchée par une file d’attente, exécutez :

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>Exécuter des fonctions localement

Pour exécuter un projet Functions, exécutez l’hôte Functions. L’hôte active les déclencheurs pour toutes les fonctions du projet :

```
func host start
```

Vous pouvez utiliser les options suivantes avec `func host start` :

- `--port [-p]`. Port local à écouter. Valeur par défaut : 7071.
- `--debug <type>`. Les options sont VSCode et VS.
- `--cors`. Liste séparée par des virgules d’origines CORS, sans espaces.
- `--nodeDebugPort [-n]`. Port du débogueur de nœud à utiliser. Valeur par défaut : une valeur issue de launch.json ou 5858.
- `--debugLevel [-d]`. Niveau de trace de la console (off, verbose, info, warning ou error). Valeur par défaut : info.
- `--timeout [-t]`. Délai d’expiration pour le démarrage de l’hôte Functions, en secondes. Valeur par défaut : 20 secondes.
- `--useHttps`. Établir une liaison avec https://localhost:{port} plutôt qu’avec http://localhost:{port}. Par défaut, cette option crée un certificat de confiance sur votre ordinateur.
- `--pause-on-error`. Marquage d’une pause pour des entrées supplémentaires avant de quitter le processus. Utile au lancement d’Azure Functions Core Tools à partir d’un environnement de développement intégré (IDE).

Quand l’hôte Functions démarre, il génère l’URL des fonctions déclenchées par HTTP :

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>Déboguer

Pour joindre un débogueur, passez l’argument `--debug`. Pour déboguer des fonctions JavaScript, utilisez Visual Studio Code. Pour les fonctions C#, utilisez Visual Studio.

Pour déboguer des fonctions C#, utilisez `--debug vs`. Vous pouvez également utiliser [Visual Studio 2017 Tools pour Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Pour lancer l’hôte et configurer le débogage JavaScript, exécutez :

```
func host start --debug vscode
```

Ensuite, dans Visual Studio Code, dans la vue **Déboguer**, sélectionnez **Attacher à Azure Functions**. Vous pouvez joindre des points d’arrêt, inspecter des variables et parcourir le code.

![Débogage JavaScript avec Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>Appeler une fonction à l’aide de données de test

Vous pouvez également appeler une fonction directement à l’aide de `func run <FunctionName>`. Cette commande est similaire à l’onglet **Tester** du portail Azure, dans lequel vous pouvez fournir des données d’entrée pour la fonction. Cette commande lance l’intégralité de l’hôte Functions.

Vous pouvez utiliser les options suivantes avec `func run` :

- `--content [-c]`. Contenu inclus.
- `--debug [-d]`. Joindre un débogueur au processus hôte avant d’exécuter la fonction.
- `--timeout [-t]`. Délai d’attente (en secondes) jusqu’à ce que l’hôte Functions local soit prêt.
- `--file [-f]`. Nom du fichier à utiliser en tant que contenu.
- `--no-interactive`. Ne pas demander d’entrée. Utile pour les scénarios d’automatisation.

Par exemple, pour appeler une fonction déclenchée par HTTP et passer un corps de contenu, exécutez la commande suivante :

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>Publier une application de fonction

Pour publier un projet Functions dans une application de fonction au sein d’Azure, utilisez la commande `publish` :

```
func azure functionapp publish <FunctionAppName>
```

Vous pouvez utiliser les options suivantes :

- `--publish-local-settings [-i]`.  Publier dans Azure les paramètres figurant dans local.settings.json, avec demande de confirmation du remplacement si le paramètre existe déjà.
- `--overwrite-settings [-y]`. Doit être utilisé avec `-i`. Remplace les paramètres d’application dans Azure par la valeur locale s’ils sont différents. Par défaut, l’accord de l’utilisateur est sollicité.

La commande `publish` charge le contenu du répertoire du projet Functions. Si vous supprimez les fichiers localement, cette commande ne les supprime pas d’Azure. Pour supprimer ces fichiers, dans le portail Azure Functions, utilisez Kudu. Pour démarrer Kudu, dans le portail Azure Functions, sélectionnez **Fonctionnalités de la plateforme** > **Outils avancés (Kudu)**. 


<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools

