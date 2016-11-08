---
title: Surveillance d’un site SharePoint avec Application Insights
description: Démarrage de la surveillance d'une nouvelle application avec une nouvelle clé d'instrumentation
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: awills

---
# Surveillance d’un site SharePoint avec Application Insights
Visual Studio Application Insights surveille la disponibilité, les performances et l’utilisation de vos applications. Vous allez maintenant apprendre à le configurer pour un site SharePoint.

## Création d’une ressource Application Insights dans Azure
Dans le [portail Azure](https://portal.azure.com), créez une ressource Application Insights. Choisissez le type d'application ASP.NET.

![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-sharepoint/01-new.png)

Dans le panneau qui s’ouvre, vous trouverez des données relatives à l’utilisation et aux performances de votre application. Vous devriez trouver une vignette sur l’écran d’accueil pour accéder à ces informations, la prochaine fois que vous vous connecterez à Azure. Sinon, cliquez sur Parcourir.

## Ajout de notre script dans vos pages web
Dans Démarrage rapide, récupérez le script pour les pages Web :

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Insérez-le juste avant la balise &lt;/head&gt; de chaque page que vous souhaitez suivre. Si votre site Web possède une page maître, vous pouvez y placer le script. Par exemple, dans un projet ASP.NET MVC, vous placeriez le script dans View\\Shared\_Layout.cshtml

Le script contient la clé d'instrumentation qui dirige la télémétrie vers votre ressource Application Insights.

### Ajouter le code aux pages de votre site
#### Sur la page maître
Si vous pouvez modifier la page maître de votre site, vous pourrez surveiller chaque page du site.

Vérifiez la page maître et modifiez-la à l'aide de SharePoint Designer ou de n’importe quel autre éditeur.

![](./media/app-insights-sharepoint/03-master.png)

Ajoutez le code juste avant la balise </head>.

![](./media/app-insights-sharepoint/04-code.png)

#### Ou sur des pages individuelles
Pour surveiller un ensemble limité de pages, ajoutez le script à chaque page, séparément.

Insérez un composant Web et incorporez l'extrait de code qu'il contient.

![](./media/app-insights-sharepoint/05-page.png)

## Affichage des données relatives à votre application
Redéployez votre application.

Revenez au panneau de votre application dans le [portail Azure](https://portal.azure.com).

Les premiers événements s’affichent dans Search.

![](./media/app-insights-sharepoint/09-search.png)

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

Dans le panneau de présentation, cliquez sur **Analyse de l’utilisation** pour accéder aux graphiques des utilisateurs, des sessions et des affichages de page :

![](./media/app-insights-sharepoint/06-usage.png)

Par exemple, cliquez sur un graphique, comme celui nommé Affichages de page, pour plus d’informations :

![](./media/app-insights-sharepoint/07-pages.png)

Ou bien Utilisateurs :

![](./media/app-insights-sharepoint/08-users.png)

## Capture des ID d’utilisateur
L’extrait de code d’une page Web standard ne capture pas l’ID d’utilisateur à partir de SharePoint, mais vous pouvez y parvenir en effectuant une simple modification.

1. Copiez la clé d’instrumentation de votre application de la liste déroulante Bases dans Application Insights. 

    ![](./media/app-insights-sharepoint/02-props.png)

1. Insérez la clé d’instrumentation à l’emplacement « XXXX » dans l’extrait de code ci-dessous.
2. Incorporez dans votre application SharePoint au lieu de l’extrait de code que vous obtenez à partir du portail.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## Étapes suivantes
* [Tests Web](app-insights-monitor-web-app-availability.md) pour surveiller la disponibilité de votre site.
* [Application Insights](app-insights-overview.md) pour les autres types d'applications.

<!--Link references-->




<!---HONumber=AcomDC_0608_2016-->