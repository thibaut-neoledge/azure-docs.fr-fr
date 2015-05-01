<properties 
	pageTitle="Vue d'ensemble d'Azure Media Services" 
	description="Cette rubrique offre une vue d'ensemble d'Azure Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

# Vue d'ensemble d'Azure Media Services

Microsoft Azure Media Services est une plateforme extensible basée sur le cloud qui permet aux développeurs de créer des applications évolutives de gestion et de diffusion de médias. Media Services est basé sur les API REST qui permettent de télécharger, stocker, encoder et empaqueter en toute sécurité du contenu vidéo ou audio destiné à être diffusé à la demande ou en direct sur différents clients (par exemple, téléviseurs, PC et appareils mobiles).

Vous pouvez créer des flux de travail de bout en bout en utilisant uniquement Media Services. Vous pouvez aussi choisir d'utiliser des composants tiers pour certaines parties de votre flux de travail (par exemple, en encodant avec un encodeur tiers). Le contenu est ensuite téléchargé, protégé, empaqueté et remis à l'aide de Media Services.

Pour créer des solutions Media Services, vous pouvez utiliser les composants suivants :

- [API REST Media Services](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Un des Kits de développement logiciel (SDK) de client disponibles : [SDK Azure Media Services pour .NET](https://github.com/Azure/azure-sdk-for-media-services), [SDK Azure pour Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services pour Node.js](https://github.com/fritzy/node-azure-media), [SDK PHP Azure](https://github.com/Azure/azure-sdk-for-php)
- Outils existants : [Portail de gestion Azure](http://manage.windowsazure.com/) ou [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**Contrat de niveau de service (SLA)** : Media Services garantit une disponibilité de 99,9 % des transactions d'API REST pour l'encodage Media Services. La diffusion en continu à la demande traitera avec succès les demandes de service avec une garantie de disponibilité de 99,9 % pour le contenu multimédia existant quand au moins une unité réservée de diffusion en continu est achetée. La disponibilité est calculée sur un cycle de facturation mensuel. Pour plus d'informations, téléchargez le [document SLA](https://www.microsoft.com/download/details.aspx?id=39302).

L'affiche suivante présente les flux de travail Azure Media Services, de la création des médias à leur consommation. Vous pouvez la télécharger ici : [Affiche Azure Media Services](http://www.microsoft.com/download/details.aspx?id=38195).

![Overview][overview]

## Concepts

Pour plus d'informations, consultez la page [Concepts](media-services-concepts.md).

## Choix de votre expérience multimédia

Une des premières étapes du partage de contenu vidéo consiste à décider quel type d'expérience vous voulez proposer à vos clients. Comment vos clients visualiseront-ils le contenu vidéo ? Seront-ils connectés à Internet ? Visionneront-ils votre contenu sur un ordinateur ou sur un appareil de poche ? Vos clients espèrent-ils visionner une vidéo haute définition ? C'est en vous posant ce genre de question que vous parviendrez à faire vivre à vos clients la meilleure expérience possible.

### Accès aux vidéos
 
Globalement, les clients ont quatre manières différentes d'accéder aux vidéos :

- Visualisation hors connexion 
- Téléchargement progressif
- Diffusion en continu\Diffusion en continu à débit adaptatif

#### Visualisation hors connexion

Pour visualiser une vidéo en mode hors connexion, le client doit télécharger la vidéo en entier sur son ordinateur ou appareil. Les vidéos ayant tendance à être assez volumineuses, le téléchargement peut prendre un certain temps. L'avantage d'une visualisation hors connexion est qu'il n'y a pas besoin de connexion réseau pour visualiser la vidéo une fois qu'elle est téléchargée sur l'appareil. 

#### Téléchargement progressif

Le téléchargement progressif impose au client d'être connecté à Internet, mais il peut commencer à regarder la vidéo avant le téléchargement complet de la vidéo. Que ce soit avec la visualisation hors connexion ou le téléchargement progressif, l'appareil dont se sert le client pour visualiser la vidéo doit avoir suffisamment d'espace de stockage pour accueillir la vidéo entière.

#### Diffusion en continu

Si les technologies de diffusion en continu requièrent aussi une connexion Internet, elles téléchargent une petite partie de la vidéo à la fois et la supprime dès qu'elle a été affichée. Elles demandent très peu d'espace de stockage sur l'appareil de visualisation. Si le débit d'une connexion réseau peut varier, les clients peuvent néanmoins espérer pouvoir visualiser les vidéos, quelle que soit la bande passante réseau disponible. Les technologies à débit adaptatif permettent aux applications de lecteur vidéo de déterminer les conditions réseau et de choisir entre plusieurs débits. Si la communication réseau se dégrade, le client peut sélectionner un débit inférieur, ce qui permet au lecteur de continuer à lire la vidéo, au détriment cependant de la qualité vidéo. Dès que les conditions réseau s'améliorent, le client peut passer à un débit binaire supérieur pour une meilleure qualité vidéo. Azure Media Services prend en charge les technologies à débit adaptatif suivantes : diffusion en continu HTTP(HLS), diffusion en continu lisse (Smooth Streaming), MPEG DASH et HDS.

### Sur quels appareils

L'autre décision à prendre concerne le type d'appareil à cibler, à savoir, le type d'appareil que les clients utiliseront pour visualiser vos vidéos. Media Services prend en charge les navigateurs web, smartphones, tablettes, XBOX, décodeurs et téléviseurs connectés.

#### Navigateurs web

Les navigateurs web peuvent s'exécuter sur les PC Windows, les Macintosh et les smartphones. En utilisant un PC ou un Macintosh, vous pouvez profiter de la taille de l'écran et de sa capacité de stockage. Cela vous permet de diffuser des vidéos de qualité supérieure. Les PC Windows ou les Macintosh peuvent lire les vidéos remises par Media Services à l'aide d'une application native ou d'un navigateur web compatible HTML. Les applications natives peuvent prendre en charge la diffusion en continu lisse, la technologie Apple HLS, le téléchargement progressif ou la visualisation hors connexion. Les pages web HTML5 prennent en charge le téléchargement progressif.


#### Smartphones

Les smartphones sont dotés de petits écrans et de plus faibles capacités de stockage. La diffusion en continu est le meilleur choix pour ces appareils. Les iPhone, Windows Phone et téléphones Android sont pris en charge. Les iPhone et téléphones Android prennent en charge la diffusion en continu lisse et la technologie HLS. Les Windows Phone prennent en charge la diffusion en continu lisse.

### Tablettes

Les tablettes ont des écrans plus grands que les smartphones, mais disposent généralement d'une moindre capacité de stockage. La diffusion en continu est le meilleur choix pour les tablettes. Les tablettes dotées de capacités de stockage supérieures peuvent aussi bénéficier de la visualisation hors connexion et du téléchargement progressif.

#### XBox

Les consoles XBox bénéficient de grands écrans et d'une capacité de stockage supérieure, ce qui les rend parfaitement compatibles avec la visualisation hors connexion, le téléchargement progressif et la diffusion en continu .
Décodeurs et téléviseurs connectés
Bien que bénéficiant d'écrans de grande taille, ces appareils ont une capacité de stockage minime, ce qui fait de la diffusion en continu le meilleur choix.

### Technologies prises en charge par appareil

Le tableau suivant présente chaque type d'appareil, ainsi que les technologies clientes pris en charge par Media Services :
 
<table border="1">
<tr><th>Appareil</th><th>Technologies</th></tr>
<tr><td>iOS</td><td>Diffusion en continu lisse, Apple HLS, téléchargement progressif</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Diffusion en continu lisse, téléchargement progressif</td></tr>
<tr><td>Téléphones Android</td><td>Diffusion en continu lisse et Apple HLS</td></tr>
<tr><td>XBox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>Apple HTTP, téléchargement progressif</td></tr>
<tr><td>Décodeur, téléviseur connecté</td><td>Diffusion en continu lisse, Apple HLS, téléchargement progressif</td></tr>
</table>


## Distribution de médias à la demande avec Azure Media Services

Pour plus d'informations, consultez la page [Distribution de médias à la demande avec Azure Media Services](media-services-video-on-demand-workflow.md).

## Diffusion vidéo en flux continu avec Azure Media Services

Pour plus d'informations, consultez la page [Diffusion vidéo en flux continu avec Azure Media Services](media-services-live-streaming-workflow.md), qui contient des liens vers des rubriques expliquant comment effectuer les tâches mentionnées ci-dessus.

## Clients

Azure Media Services fournit les outils dont vous avez besoin pour créer des applications de lecteur clientes riches et dynamiques pour la plupart des plateformes, notamment : appareils iOS, Android, Windows, Windows Phone, Xbox et décodeurs.

- [Kit de développement logiciel (SDK) de client de diffusion en continu lisse](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Plateforme multimédia Microsoft : infrastructure de lecteur](http://playerframework.codeplex.com/) 
- [Documentation de l'infrastructure de lecteur HTML5](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Plug-in de diffusion en continu lisse Microsoft pour OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Infrastructure de lecteur multimédia pour iOS](https://github.com/Azure/azure-media-player-framework) 
- [Licence du kit de portage du client de diffusion en continu lisse Microsoft(r)](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- Création d'applications vidéo sur Windows 8 
- [Développement d'applications vidéo pour XBOX](http://xbox.create.msdn.com/) 

Pour plus d'informations, consultez la page [Développement d'applications vidéo pour XBOX](media-services-develop-video-players.md)

## Modèles et pratiques recommandées

[Modèles et pratiques recommandées](https://wamsg.codeplex.com/)
[Documentation en ligne](https://msdn.microsoft.com/library/dn735912.aspx)
[Livre électronique téléchargeable](https://www.microsoft.com/download/details.aspx?id=42629)

## Support

Le [support Azure](http://azure.microsoft.com/support/options/) propose des options de support pour Azure, y compris Media Services.



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png


<!--HONumber=52-->