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
ms.date: 07/12/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Coder et tester des fonctions Azure localement

Bien que le [portail Azure] fournisse un ensemble complet d’outils pour le développement et le test d’Azure Functions, nombreux sont les développeurs qui privilégient une expérience de développement local. Azure Functions facilite l’utilisation de votre éditeur de code et de vos outils de développement local préférés pour développer et tester vos fonctions sur votre ordinateur local. Vos fonctions peuvent se déclencher sur des événements dans Azure, et vous pouvez déboguer vos fonctions C# et JavaScript sur votre ordinateur local. 

Si vous êtes un développeur Visual Studio C#, Azure Functions [s’intègre aussi à Visual Studio 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Installer Azure Functions Core Tools

Azure Functions Core Tools est une version locale du runtime Azure Functions que vous pouvez exécuter sur votre ordinateur Windows local. Ce n’est pas un émulateur ni un simulateur. Il s’agit du même runtime que celui qui alimente les fonctions dans Azure.

[Azure Functions Core Tools] est fourni en tant que package npm. Vous devez d’abord [installer NodeJS](https://docs.npmjs.com/getting-started/installing-node), qui inclut npm.  

>[!NOTE]
>À ce stade, le package Azure Functions Core Tools ne peut être installé que sur les ordinateurs Windows. Cette restriction est due à une limitation temporaire dans l’hôte d’Azure Functions.

[Azure Functions Core Tools] ajoute les alias de commande suivants :
* **func**
* **azfun**
* **azurefunctions**

Ces alias peuvent être utilisés à la place de `func` indiqué dans les exemples de cette rubrique.

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
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>", 
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
| **Valeurs** | Collection de paramètres d’application utilisés lors de l’exécution locale. Ajoutez les paramètres de votre application à cet objet.  |
| **AzureWebJobsStorage** | Définit la chaîne de connexion sur le compte Stockage Azure qui est utilisé en interne par le runtime Azure Functions. Le compte de stockage prend en charge les déclencheurs de votre fonction. Ce paramètre de connexion du compte de stockage est requis pour toutes les fonctions à l’exception des fonctions déclenchées par HTTP.  |
| **AzureWebJobsDashboard** | Définit la chaîne de connexion sur le compte Stockage Azure qui est utilisé pour stocker les journaux de fonction. Cette valeur facultative rend les journaux accessibles dans le portail.|
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

Pour définir une valeur pour des chaînes de connexion, vous pouvez effectuer une des opérations suivantes :
* Entrez la chaîne de connexion à partir de [l’Explorateur de stockage Azure](http://storageexplorer.com/).
* Utilisez l’une des commandes suivantes :

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
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

La commande `publish` charge le contenu du répertoire du projet Functions. Si vous supprimez les fichiers localement, la commande `publish` ne les supprime pas d’Azure. Vous pouvez supprimer des fichiers dans Azure à l’aide de [l’outil Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) dans le [portail Azure].

## <a name="next-steps"></a>Étapes suivantes

Azure Functions Core Tools est [disponible en open source et hébergé sur GitHub](https://github.com/azure/azure-functions-cli).  
Pour enregistrer un bogue ou une demande de fonctionnalité, [créez un problème GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[portail Azure]: https://portal.azure.com 

