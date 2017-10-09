---
title: "Développer et exécuter des fonctions Azure en local | Microsoft Docs"
description: "Apprenez à coder et à tester des fonctions Azure sur votre ordinateur local avant de les exécuter dans Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 38f6f5ebe0c53bc4314fa11f0f8d4f00af6086dd
ms.contentlocale: fr-fr
ms.lasthandoff: 09/29/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Coder et tester Azure Functions localement

Bien que le [portail Azure] fournisse un ensemble complet d’outils pour le développement et le test d’Azure Functions, nombreux sont les développeurs qui privilégient une expérience de développement local. Azure Functions facilite l’utilisation de votre éditeur de code et de vos outils de développement local préférés pour développer et tester vos fonctions sur votre ordinateur local. Vos fonctions peuvent se déclencher sur des événements dans Azure, et vous pouvez déboguer vos fonctions C# et JavaScript sur votre ordinateur local. 

Si vous êtes un développeur Visual Studio C#, Azure Functions [s’intègre aussi à Visual Studio 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Installer Azure Functions Core Tools

[Azure Functions Core Tools] est une version locale du runtime d’Azure Functions que vous pouvez exécuter sur votre ordinateur de développement local. Ce n’est pas un émulateur ni un simulateur. Il s’agit du même runtime que celui qui alimente les fonctions dans Azure. Il existe deux versions d’Azure Functions Core Tools, l’une destinée à la version 1.x du runtime et l’autre à la version 2.x. Les deux versions sont fournies en tant que [package npm](https://docs.npmjs.com/getting-started/what-is-npm).

>[!NOTE]  
> Avant d’installer l’une ou l’autre des versions, vous devez [installer NodeJS](https://docs.npmjs.com/getting-started/installing-node), qui inclut npm. Pour la version 2.x des outils, seuls Node.js 8.5 et les versions ultérieures sont pris en charge. 

### <a name="version-1x-runtime"></a>Runtime de la version 1.x

La version d’origine des outils utilise le runtime de Functions 1.x. Cette version utilise .NET Framework et n’est prise en charge que sur les ordinateurs Windows. Pour installer les outils de la version 1.x, utilisez la commande suivante :

```bash
npm install -g azure-functions-core-tools
```

### <a name="version-2x-runtime"></a>Runtime de la version 2.x

La version 2.x des outils utilise le runtime d’Azure Functions 2.x qui repose sur .NET Core. Cette version est prise en charge sur toutes les plateformes .NET Core prises en charge par la version 2.x. Utilisez cette version pour le développement multiplateforme et lorsque le runtime de Functions 2.x est requis. 

>[!IMPORTANT]   
> Avant d’installer Azure Functions Core Tools, [installez .NET Core 2.0](https://www.microsoft.com/net/core).  
>
> Le runtime d’Azure Functions 2.0 est en préversion ; pour le moment, toutes les fonctionnalités d’Azure Functions ne sont pas prises en charge. Pour plus d’informations, consultez l’article [Azure Functions runtime 2.0 known issues](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) (Problèmes connus du runtime d’Azure Functions 2.0). 

 Pour installer les outils de la version 2.0, utilisez la commande suivante :

```bash
npm install -g azure-functions-core-tools@core
```

Lorsque vous effectuez une installation sur Ubuntu, utilisez `sudo` comme suit :

```bash
sudo npm install -g azure-functions-core-tools@core
```

Lorsque vous procédez à une installation sur macOS et Linux, il se peut que vous deviez inclure l’indicateur `unsafe-perm`, comme suit :

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

## <a name="run-azure-functions-core-tools"></a>Exécuter Azure Functions Core Tools
 
Azure Functions Core Tools ajoute les alias de commande suivants :
* **func**
* **azfun**
* **azurefunctions**

Ces alias peuvent être utilisés là où `func` est indiqué dans les exemples.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Créer un projet Functions local

Quand il est exécuté localement, un projet Functions est un répertoire qui contient les fichiers [host.json](functions-host-json.md) et [local.settings.json](#local-settings). Ce répertoire est l’équivalent d’une application de fonction dans Azure. Pour plus d’informations sur la structure de dossiers Azure Functions, consultez le [Guide de développement Azure Functions](functions-reference.md#folder-structure).

Dans la fenêtre du terminal ou à partir d’une invite de commandes, exécutez la commande suivante pour créer le projet et le référentiel Git local :

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

Pour créer le projet sans référentiel Git local, utilisez l’option `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Fichier de paramètres locaux

Le fichier local.settings.json stocke les paramètres de l’application, les chaînes de connexion et les paramètres d’Azure Functions Core Tools. Sa structure est la suivante :

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Paramètre      | Description                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Lorsque la valeur est définie sur **true**, toutes les valeurs sont chiffrées à l’aide d’une clé d’ordinateur local. Utilisé avec les commandes `func settings`. La valeur par défaut est **false**. |
| **Valeurs** | Collection de paramètres d’application utilisés lors de l’exécution locale. **AzureWebJobsStorage** et **AzureWebJobsDashboard** en sont des exemples. Pour obtenir leur liste complète, consultez les [informations de référence sur les paramètres application](functions-app-settings.md).  |
| **Hôte** | Les paramètres de cette section personnalisent le processus hôte Functions lors de l’exécution locale. | 
| **LocalHttpPort** | Définit le port par défaut utilisé lors de l’exécution de l’hôte Functions local (`func host start` et `func run`). L’option de ligne de commande `--port` est prioritaire sur cette valeur. |
| **CORS** | Définit les origines autorisées pour [cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Les origines sont fournies sous la forme d’une liste séparée par des virgules, sans espaces. La valeur de caractère générique (**\***) est prise en charge, ce qui autorise les demandes à partir de n’importe quelle origine. |
| **ConnectionStrings** | Contient les chaînes de connexion de base de données pour vos fonctions. Les chaînes de connexion dans cet objet sont ajoutées à l’environnement avec le type de fournisseur **System.Data.SqlClient**.  | 

La plupart des déclencheurs et des liaisons ont une propriété **Connection** qui correspond au nom d’une variable d’environnement ou d’un paramètre d’application. Pour chaque propriété de connexion, un paramètre d’application doit être défini dans le fichier local.settings.json. 

Ces paramètres peuvent aussi être lus dans votre code en tant que variables d’environnement. Dans C#, utilisez [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) ou [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). En JavaScript, utilisez `process.env`. Les paramètres spécifiés comme variable d’environnement système sont prioritaires sur les valeurs contenues dans le fichier local.settings.json. 

Les paramètres dans le fichier local.settings.json sont uniquement utilisés par les outils Functions lors de l’exécution locale. Par défaut, ces paramètres ne sont pas migrés automatiquement lorsque le projet est publié dans Azure. Utilisez le commutateur `--publish-local-settings` [lors de la publication](#publish) pour vous assurer que ces paramètres sont ajoutés à l’application de fonction dans Azure.

Si aucune chaîne de connexion de stockage valide n’est définie pour **AzureWebJobsStorage**, le message d’erreur suivant s’affiche :  

>Valeur manquante pour AzureWebJobsStorage dans local.settings.json. Cette valeur est nécessaire pour tous les déclencheurs autres que HTTP. Vous pouvez exécuter 'func azure functionary fetch-app-settings <functionAppName>' ou spécifier une chaîne de connexion dans local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Configuration des paramètres d’application

Pour définir une valeur pour des chaînes de connexion, vous avez le choix suivant :
* Entrez la chaîne de connexion à partir de [l’Explorateur de stockage Azure](http://storageexplorer.com/).
* Utilisez l’une des commandes suivantes :

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Les deux commandes nécessitent que vous vous connectiez d’abord à Azure.

## <a name="create-a-function"></a>Créer une fonction

Exécutez la commande suivante pour créer une fonction :

```
func new
``` 
`func new` prend en charge les arguments facultatifs suivants :

| Argument     | Description                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Langage de programmation du modèle, tel que C#, F# ou JavaScript. |
| **`--template -t`** | Nom du modèle. |
| **`--name -n`** | Nom de la fonction. |

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

`func host start` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Port local à écouter. Valeur par défaut : 7071. |
| **`--debug <type>`** | Les options sont `VSCode` et `VS`. |
| **`--cors`** | Liste séparée par des virgules d’origines CORS, sans espaces. |
| **`--nodeDebugPort -n`** | Port du débogueur de nœud à utiliser. Valeur par défaut : une valeur issue de launch.json ou 5858. |
| **`--debugLevel -d`** | Niveau de trace de la console (off, verbose, info, warning ou error). Valeur par défaut : info.|
| **`--timeout -t`** | Délai d’expiration pour le démarrage de l’hôte Functions, en secondes. Valeur par défaut : 20 secondes.|
| **`--useHttps`** | Établir une liaison avec https://localhost:{port} plutôt qu’avec http://localhost:{port}. Par défaut, cette option crée un certificat de confiance sur votre ordinateur.|
| **`--pause-on-error`** | Marquage d’une pause pour des entrées supplémentaires avant de quitter le processus. Utile au lancement d’Azure Functions Core Tools à partir d’un environnement de développement intégré (IDE).|

Quand l’hôte Functions démarre, il génère l’URL des fonctions déclenchées par HTTP :

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Débogage dans VS Code ou Visual Studio

Pour joindre un débogueur, passez l’argument `--debug`. Pour déboguer des fonctions JavaScript, utilisez Visual Studio Code. Pour les fonctions C#, utilisez Visual Studio.

Pour déboguer des fonctions C#, utilisez `--debug vs`. Vous pouvez également utiliser [Visual Studio 2017 Tools pour Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Pour lancer l’hôte et configurer le débogage JavaScript, exécutez :

```
func host start --debug vscode
```

Ensuite, dans Visual Studio Code, dans la vue **Déboguer**, sélectionnez **Attacher à Azure Functions**. Vous pouvez joindre des points d’arrêt, inspecter des variables et parcourir le code.

![Débogage JavaScript avec Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Transmission de données de test à une fonction

Vous pouvez également appeler une fonction directement à l’aide de `func run <FunctionName>` et fournir des données d’entrée pour la fonction. Cette commande est similaire à l’exécution d’une fonction à l’aide de l’onglet **Test** dans le portail Azure. Cette commande lance l’intégralité de l’hôte Functions.

`func run` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Contenu inclus. |
| **`--debug -d`** | Joindre un débogueur au processus hôte avant d’exécuter la fonction.|
| **`--timeout -t`** | Délai d’attente (en secondes) jusqu’à ce que l’hôte Functions local soit prêt.|
| **`--file -f`** | Nom du fichier à utiliser en tant que contenu.|
| **`--no-interactive`** | Ne pas demander d’entrée. Utile pour les scénarios d’automatisation.|

Par exemple, pour appeler une fonction déclenchée par HTTP et passer un corps de contenu, exécutez la commande suivante :

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publication dans Azure

Pour publier un projet Functions dans une application de fonction au sein d’Azure, utilisez la commande `publish` :

```
func azure functionapp publish <FunctionAppName>
```

Vous pouvez utiliser les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publier dans Azure les paramètres figurant dans local.settings.json, avec demande de confirmation du remplacement si le paramètre existe déjà.|
| **`--overwrite-settings -y`** | Doit être utilisé avec `-i`. Remplace les paramètres d’application dans Azure par la valeur locale s’ils sont différents. Par défaut, l’accord de l’utilisateur est sollicité.|

Cette commande publie du contenu vers une application de fonction existante dans Azure. Une erreur se produit si le `<FunctionAppName>` n’existe pas dans votre abonnement. Pour découvrir comment créer une application de fonction à partir de l’invite de commandes ou d’une fenêtre de terminal à l’aide d’Azure CLI, consultez l’article [Créer une Function App pour une exécution sans serveur](./scripts/functions-cli-create-serverless.md).

La commande `publish` charge le contenu du répertoire du projet Functions. Si vous supprimez les fichiers localement, la commande `publish` ne les supprime pas d’Azure. Vous pouvez supprimer des fichiers dans Azure à l’aide de [l’outil Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) dans le [portail Azure].  

>[!IMPORTANT]  
> Lorsque vous créez une application de fonction dans Azure, elle utilise par défaut la version 1.x du runtime de Function. Pour que l’application de fonction utilise la version 2.x du runtime, ajoutez le paramètre d’application `FUNCTIONS_EXTENSION_VERSION=beta`.  
Le code d’Azure CLI suivant permet d’ajouter ce paramètre à votre application de fonction : 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Étapes suivantes

Azure Functions Core Tools est [disponible en open source et hébergé sur GitHub](https://github.com/azure/azure-functions-cli).  
Pour enregistrer un bogue ou une demande de fonctionnalité, [créez un problème GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[portail Azure]: https://portal.azure.com 

