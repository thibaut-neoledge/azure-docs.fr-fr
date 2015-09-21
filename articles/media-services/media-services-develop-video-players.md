<properties 
	pageTitle="Développement d’applications de lecteur vidéo" 
	description="La rubrique fournit des liens vers les infrastructures de lecteur et les plug-ins que vous pouvez utiliser pour développer vos propres applications clientes qui permettront de faire appel à la diffusion multimédia en continu à partir de Media Services." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#Développement d’applications de lecteur vidéo

##Vue d'ensemble

Azure Media Services fournit les outils dont vous avez besoin pour créer des applications de lecteur clientes riches et dynamiques pour la plupart des plateformes, notamment : appareils iOS, Android, Windows, Windows Phone, Xbox et décodeurs. Cette rubrique fournit également des liens vers les Kits de développement logiciel (SDK) et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes afin d’utiliser la diffusion en continu de médias à partir d’Azure Media Services.


##Azure Media Player

[Azure Media Player](http://aka.ms/ampinfo) est un lecteur vidéo web conçu pour lire du contenu multimédia à partir de Microsoft Azure Media Services sur une grande variété de navigateurs et d’appareils. Azure Media Player exploite des normes du secteur, telles que HTML5, Media Source Extensions (MSE) et Encrypted Media Extensions (EME), pour offrir une expérience enrichie de diffusion en continu adaptative. Lorsque ces normes ne sont pas disponibles sur un périphérique ou dans un navigateur, Azure Media Player utilise Flash et Silverlight comme technologies de secours. Quelle que soit la technologie de lecture utilisée, les développeurs bénéficient d’une interface JavaScript unifiée pour accéder aux API. Le contenu fourni par Azure Media Services peut ainsi être lu sur un large éventail de périphériques et de navigateurs sans effort supplémentaire.

Microsoft Azure Media Services permet la distribution du contenu dans les formats de diffusion en continu DASH, Smooth Streaming et HLS pour la lecture. Azure Media Player prend en compte des différents formats et lit automatiquement le lien le mieux adapté aux capacités de la plateforme/du navigateur. Microsoft Azure Media Services assure également le chiffrement dynamique des ressources avec le chiffrement PlayReady ou le chiffrement d’enveloppe AES 128 bits. Lorsqu’il est configuré de manière appropriée, Azure Media Player permet le déchiffrement du contenu chiffré avec PlayReady et AES 128 bits.

Pour en savoir plus :

- [Azure Media Player](http://aka.ms/ampinfo)
- [Documentation d’Azure Media Player](http://aka.ms/ampdocs) 
- [Blog de prise en main d’Azure Media Player](http://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player)
- [Inscrivez-vous pour vous tenir informé des dernières informations concernant Azure Media Player](http://aka.ms/ampsignup)
- [Ajouter de nouvelles demandes de fonctionnalités, des idées et des commentaires](http://feedback.azure.com/forums/293565-azure-media-player/filters/my_feedback) 


##Autres outils pour la création d’applications de lecteur

Vous pouvez également utiliser un des Kits de développement logiciel (SDK) suivants :

- [Kit de développement logiciel (SDK) de client de diffusion en continu lisse](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Didacticiel d’application Windows Store de diffusion en continu lisse](media-services-build-smooth-streaming-apps.md)
- [Plateforme multimédia Microsoft : infrastructure de lecteur](http://playerframework.codeplex.com/) 
- [Documentation de l’infrastructure de lecteur HTML5](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Plug-in de diffusion en continu lisse Microsoft pour OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licence du kit de portage du client de diffusion en continu lisse Microsoft®](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- [Développement d’applications vidéo pour XBOX](http://xbox.create.msdn.com/) 
 

##Publicité

Azure Media Services assure la prise en charge des insertions publicitaires via la plateforme Windows Media : Infrastructures de lecteur. Des infrastructures de lecteur avec prise en charge des publicités sont disponibles pour les périphériques Windows 8, Silverlight, Windows Phone 8 et iOS. Chaque infrastructure de lecteur contient un exemple de code qui montre comment implémenter une application de lecteur. Il existe trois sortes de publicités différentes que vous pouvez insérer dans votre contenu multimédia :

Linéaire : publicité en plein cadre qui interrompt la vidéo principale

Non linéaire : publicité superposée qui s’affiche pendant la lecture de la vidéo principale ; il s’agit généralement d’un logo ou autre image statique apparaissant à l’intérieur du cadre du lecteur

Compagnon : publicité d’accompagnement qui s’affiche hors du cadre du lecteur

Les publicités peuvent être placées à n’importe quel point dans la chronologie de la vidéo principale. Vous devez indiquer au lecteur les publicités à diffuser, ainsi que le moment auquel le faire. Cela s’effectue via un ensemble de fichiers XML standard : Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) et Digital Video Player Ad Interface Definition (VPAID). Les fichiers VAST spécifient les publicités à afficher. Les fichiers VMAP indiquent quand diffuser les différentes publicités ; ils contiennent le code XML VAST. Les fichiers MAST permettent également de séquencer des publicités contenant aussi du code XML VAST. Les fichiers VPAID définissent une interface entre le lecteur vidéo et la publicité ou le serveur publicitaire. Pour plus d’informations, consultez la page [Insertion de publicités](https://msdn.microsoft.com/library/dn387398.aspx).

Pour en savoir plus sur la prise en charge du sous-titrage et des publicités dans les vidéos en flux continu, consultez la page [Normes de sous-titrage et d’insertion de publicités prises en charge](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##Parcours d’apprentissage de Media Services

Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

- [Workflow en flux continu AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Workflow de streaming à la demande AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Voir aussi

[Incorporation d'une vidéo de diffusion en continu adaptative MPEG-DASH dans une application HTML5 avec DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Référentiel dash.js GitHub](https://github.com/Dash-Industry-Forum/dash.js)
 

<!---HONumber=Sept15_HO2-->