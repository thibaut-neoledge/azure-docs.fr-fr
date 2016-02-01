<properties
   pageTitle="Exécuter des expressions C# dans une application API C# dans une application logique | Microsoft Azure"
   description="Application API C# ou connecteur"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="01/19/2016"
   ms.author="jehollan"/>

#Application API C#
L’application API C# vous permet d’exécuter aisément des expressions C# simples *pendant que votre application logique s’exécute*.

##Quand utiliser cette application API ?
Utilisez cette application API quand vous souhaitez que le cycle de vie du code que vous écrivez soit le même que celui de l’application logique, et que vous ne voulez *pas* que le code soit appelé dans d’autres scénarios.

En revanche, si vous souhaitez un extrait de code réutilisable avec un cycle de vie indépendant de celui de l’application logique, utilisez plutôt l’application API WebJobs pour créer des expressions de code simple et les appeler à partir de votre application logique.

Enfin, si vous voulez inclure des packages supplémentaires, vous devez transmettre l’assembly (.dll) au connecteur sous la forme d’une chaîne binaire encodée en base 64 (comme la sortie du stockage d’objets blob). Si vous souhaitez davantage de flexibilité au niveau des packages et des assemblys, l’utilisation d’une tâche web (WebJob) se révélera probablement plus adaptée.

Si vous préférez écrire vos expressions en JS, utilisez l’[application API JavaScript](app-service-logic-javascript-api.md).

##Création d’une application API C#
Pour utiliser l’application API C#, vous devez commencer par en créer une instance. Vous pouvez effectuer cette opération inline quand vous créez une application logique ou en sélectionnant l’application API C# à partir d’Azure Marketplace.

##Utilisation de l’application API C# dans l’aire du concepteur d’applications logiques
###Déclencheur
Vous pouvez créer un déclencheur que le service Application logique interrogera (selon un intervalle que vous définissez). Si ce déclencheur renvoie une valeur différente de `false`, l’application logique s’exécute. Dans le cas contraire, elle attend le prochain intervalle d’interrogation pour vérifier la réponse renvoyée.

Les entrées du déclencheur sont les suivantes : - **Expression C#** - Il s’agit de l’expression qui est évaluée. Elle est appelée à l’intérieur d’une fonction et doit renvoyer la valeur `false` lorsque vous ne souhaitez pas que l’application logique s’exécute, ou toute autre valeur pour que l’application logique s’exécute. Vous pouvez utiliser le contenu de la réponse dans les actions de l’application logique.

Par exemple, vous pouvez utiliser un déclencheur simple qui n’exécute votre application logique qu’entre les 15 et les 30 premières minutes de chaque heure :

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

###Action

De la même façon, vous pouvez spécifier une action à exécuter.

Les entrées de l’action sont les suivantes : - **Expression C#** - Il s’agit de l’expression qui est évaluée. Vous devez inclure l’instruction `return` pour obtenir un contenu quel qu’il soit. - **Objet(s) de contexte** - Objet de contexte facultatif pouvant être transmis dans le déclencheur. Vous pouvez définir autant de propriétés que vous le souhaitez, mais la base doit être un élément JObject `{ ... }`, et les objets peuvent être référencés dans le script par le biais du nom de clé (la valeur est transmise sous la forme d’un élément JToken correspondant au nom). - **Bibliothèques** - Groupe facultatif de fichiers .dll à inclure dans la compilation du script. Ce groupe utilise la structure ci-après et fonctionne mieux à côté d’un connecteur de stockage d’objets blob avec le fichier .dll en guise de sortie :

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

Par exemple, supposons que vous utilisiez le déclencheur d’Office 365 **Nouveau message**. Ce dernier renvoie l’objet suivant :

```javascript
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

Toutefois, vous voulez charger ces pièces jointes dans une publication Yammer. Malheureusement, le schéma des pièces jointes Yammer est légèrement différent. À présent, vous êtes en mesure d'analyser ceci dans votre application logique. Pour l’objet de contexte, transmettez simplement la chaîne : `@triggerBody()`, et pour l’expression, transmettez le code suivant :

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
	JObject att = new JObject();
	att["Content"] = obj["content"];
	att["FileName"] = obj["Name"];
	YammerAttachments.Add(att);	
}
return YammerAttachments;
```

L’action renvoie l’objet que vous avez renvoyé à partir de votre fonction dans un objet résultats. Vous pouvez alors référencer `@body('csapi').results` pour la propriété **Attachments** dans l’application API Yammer.

## En faire plus avec votre connecteur
Le connecteur étant créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0121_2016-->