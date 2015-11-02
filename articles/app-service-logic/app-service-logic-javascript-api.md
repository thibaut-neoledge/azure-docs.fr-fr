<properties
   pageTitle="API JavaScript"
   description="API JavaScript"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/15/2015"
   ms.author="stepsic"/>

#Application API JavaScript
L’application API JavaScript vous permet d’exécuter aisément des expressions JavaScript simples *pendant que votre application logique s’exécute*.

##Quand utiliser cette application API ?
Utilisez cette application API lorsque vous souhaitez que le cycle de vie du code que vous écrivez soit le même que celui de l’application logique, et que vous ne voulez *pas* que le code soit appelé dans d’autres scénarios.

En revanche, si vous souhaitez un extrait de code réutilisable avec un cycle de vie indépendant de celui de l’application logique, utilisez plutôt l’application API WebJobs pour créer des expressions de code simple et les appeler à partir de votre application logique.

Enfin, si vous voulez inclure des packages supplémentaires, vous devrez également utiliser l’application API WebJobs, car vous ne pouvez pas ajouter de bibliothèques à l’aide de l’application API JavaScript.

Si vous préférez écrire vos expressions en C#, utilisez l’[application API C#](app-service-logic-cs-api.md).

##Création d’une application API JavaScript
Pour utiliser l’application API JavaScript, vous devez commencer par en créer une instance. Vous pouvez effectuer cette opération inline quand vous créez une application logique ou en sélectionnant l’application API JavaScript à partir d’Azure Marketplace.

##Utilisation de l’application API JavaScript dans l’aire du concepteur d’applications logiques
###Déclencheur
Vous pouvez créer un déclencheur que le service Application logique interrogera (selon un intervalle que vous définissez). Si ce déclencheur renvoie un contenu quelconque, l’application logique s’exécute. Dans le cas contraire, elle attend le prochain intervalle d’interrogation pour vérifier la réponse renvoyée.

Les entrées du déclencheur sont les suivantes : - **Expression JavaScript** - Il s’agit de l’expression qui sera évaluée. Elle sera appelée à l’intérieur d’une fonction et doit renvoyer la valeur `false` lorsque vous ne souhaitez pas que l’application logique s’exécute, ou toute autre valeur pour que l’application logique s’exécute. Vous ne pourrez pas utiliser le contenu de la réponse dans les actions de l’application logique. - **Objet de contexte** - Objet facultatif pouvant être transmis dans le déclencheur. Vous pouvez définir autant de propriétés que vous le souhaitez, mais l’entité de niveau supérieur doit être un objet, par exemple `{ "bar" : 0}`.

Par exemple, vous pouvez utiliser un déclencheur simple qui n’exécutera votre application logique qu’entre les 15 et les 30 premières minutes de chaque heure :

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

###Action

De la même façon, vous pouvez spécifier une action à exécuter.

Les entrées de l’action sont les suivantes : - **Expression JavaScript** - Il s’agit de l’expression qui sera évaluée. Vous devez inclure l’instruction `return` pour obtenir un contenu quel qu’il soit. - **Objet de contexte** - Objet facultatif pouvant être transmis dans le déclencheur. Vous pouvez définir autant de propriétés que vous le souhaitez, mais l’entité de niveau supérieur doit être un objet, par exemple `{ "bar" : 0}`.

Par exemple, supposons que vous utilisiez le déclencheur d’Office 365 **Nouveau message**. Ce dernier renvoie l’objet suivant : ```
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

Toutefois, vous voulez charger ces pièces jointes dans une publication Yammer. Malheureusement, le schéma des pièces jointes Yammer est légèrement différent. Vous êtes désormais en mesure d’analyser ceci à l’intérieur de votre application logique. Pour l’objet de contexte, transmettez simplement la chaîne : `@triggerBody()`, et pour l’expression, transmettez le code suivant :

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

L’action renvoie l’élément JSON que vous avez renvoyé à partir de votre fonction. Vous pouvez alors référencer `@body('javascriptapi')` pour la propriété **Attachments** dans l’application API Yammer.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=Oct15_HO4-->