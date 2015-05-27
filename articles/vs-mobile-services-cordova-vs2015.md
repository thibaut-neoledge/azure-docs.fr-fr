<properties 
	pageTitle="" 
	description="Utilisation de Mobile Services dans les projets Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Utilisation de Mobile Services avec les projets Cordova dans Visual Studio 2015 Preview

Pour utiliser Azure Mobile Services dans les projets Cordova dans Visual Studio 2015 Preview, vous devez appliquer la solution de contournement suivante.

1. Dans le cadre de votre projet Cordova ouvert dans Visual Studio 2015 Preview, ouvrez Config.xml, puis sous l’onglet **Plug-ins**, activez le plug-in **Azure Mobile Services**.<br/> ![][1]

2. Dans index.html, supprimez les lignes qui font référence à **MobileServices.Web-1.2.2.min.js**.<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt; &lt;html&gt; &lt;head&gt; &lt;meta charset="utf-8" /&gt; &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt; &lt;body&gt; &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt; &lt;/html&gt; </PRE>

3. Ouvrez {votre_nom_de_service}.js sous les dossiers services -> mobileServices -> paramètres et remplacez l'extrait de code existant par le code suivant :

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png
<!--HONumber=54-->