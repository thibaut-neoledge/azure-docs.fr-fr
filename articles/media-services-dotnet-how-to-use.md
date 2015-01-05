<properties pageTitle="Utilisation de Media Services" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#Utilisation de Media Services

Ce guide décrit la prise en main de la programmation à l'aide d'Azure Media Services. Il comprend un ensemble d'articles ainsi qu'une présentation technique de Media Services, une procédure de configuration de votre compte Azure pour Media Services, un guide de configuration relatif au développement et des rubriques expliquant comment accomplir des tâches de programmation courantes. Les scénarios abordés sont les suivants : téléchargement, chiffrement, encodage et fourniture d'éléments multimédias. Les exemples ont été écrits en code C# et utilisent le Kit de développement logiciel (SDK) d'Azure Media Services pour .NET. Pour plus d'informations sur Azure Media Services, consultez la rubrique [Étapes suivantes][].

Vous pouvez également programmer Media Services à l'aide des API REST basées sur OData. Vous pouvez créer une application qui passe des appels d'API REST à Media Services à partir des langages .NET ou d'autres langages de programmation. Une documentation complète sur la programmation à l'aide de l'API REST de Media Services est disponible à la rubrique [Création d'applications avec l'API REST d'Azure Media Services](http://go.microsoft.com/fwlink/?linkid=252967). 

Pour programmer avec l'API REST de Media Services ou le Kit de développement logiciel (SDK) de Media Services, commencez par activer votre compte Azure pour Media Services, comme décrit à la section [Configuration d'un compte Azure pour Media Services][].

Cliquez [ici](http://msdn.microsoft.com/fr-fr/library/hh973613.aspx)pour accéder à la documentation la plus récente sur le Kit de développement logiciel (SDK) de Media Services. 

##<a name="what-are"></a><span class="short header">Présentation de Media Services</span>
Azure Media Services est une plateforme multimédia extensible qui intègre la plateforme multimédia Microsoft et les composants multimédias tiers les plus fiables au sein d'Azure. Media Services fournit dans le cloud un pipeline multimédia qui permet aux partenaires du secteur d'étendre ou de remplacer les technologies relatives aux composants. Les éditeurs de logiciels indépendants et les fournisseurs de médias peuvent utiliser Media Services pour créer des solutions multimédias de bout en bout. Cette présentation décrit l'architecture générale et les scénarios de développement courants relatifs à Media Services.

Le schéma suivant illustre l'architecture Media Services de base.

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

###Prise en charge des fonctionnalités de Media Services
Les fonctionnalités suivantes de la version actuelle de Media Services permettent de développer des applications multimédias dans le cloud.

- **Réception**. Les opérations de réception permettent d'intégrer des éléments multimédias au système, par exemple en les téléchargeant et en les chiffrant avant de les placer dans Azure Storage. L'intégration aux composants partenaires proposée par Media Services permet de fournir des solutions de téléchargement UDP (User Datagram Protocol) rapides.
- **Encodage**. Les opérations d'encodage englobent l'encodage proprement dit ainsi que la transformation et la conversion des éléments multimédias. Vous pouvez exécuter des tâches d'encodage dans le cloud via l'Encodeur multimédia Azure. Les options d'encodage disponibles sont les suivantes :
   - Utilisez l'Encodeur multimédia Azure ainsi que différents codecs et formats standard, comme la diffusion en continu lisse IIS, MP4 et la conversion au format de diffusion en continu HTTP Apple.
   - Convertissez des bibliothèques entières ou des fichiers individuels avec un contrôle total sur les entrées et sorties.
   - Un large éventail de types de fichiers, formats et codecs sont pris en charge (consultez la rubrique [Types de fichiers pris en charge pour Media Services][]).
   - Conversions aux formats pris en charge. Media Services vous permet de convertir le format ISO MP4 (.mp4) au format de fichier de diffusion en continu lisse (PIFF 1.3) (.ismv; .isma). Vous pouvez également convertir le format de fichier de diffusion en continu lisse (PIFF) au format de diffusion en continu HTTP Apple (.msu8, .ts).
- **Protection**. La protection de contenu consiste à chiffrer le contenu diffusé en continu ou à la demande afin de sécuriser son transport, son stockage et sa fourniture. Media Services fournit une solution à technologie DRM non spécifiée pour protéger le contenu.  Les technologies DRM actuellement prises en charge sont Microsoft PlayReady et MPEG CENC. D'autres technologies DRM seront bientôt disponibles. 
- **Diffusion en continu**. La diffusion en continu implique l'envoi du contenu en direct ou à la demande aux clients. Vous pouvez également télécharger des fichiers multimédias spécifiques à partir du cloud. Media Services fournit une solution au format non spécifié pour du contenu de diffusion en continu.  Media Services prend en charge l'origine de la diffusion en continu pour les formats de diffusion en continu lisse, de diffusion en continu HTTP Apple et MP4. D'autres formats seront bientôt disponibles. Vous pouvez aussi diffuser du contenu en continu en toute transparence via un réseau de distribution de contenu (CDN) tiers permettant d'atteindre des millions d'utilisateurs.   

###Scénarios de développement de Media Services
Media Services prend en charge différents scénarios de développement multimédias courants, comme décrit dans le tableau suivant. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Scénario</th>
       <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Création de workflows de bout en bout</td>
        <td>Créez des workflows multimédias complets dans le cloud. Du téléchargement de fichiers multimédias à la distribution de contenu, Media Services fournit un large éventail de composants qui peuvent être combinés pour gérer les workflows d'applications spécifiques. Les fonctionnalités actuellement disponibles sont le téléchargement, le stockage, l'encodage, la conversion de format, la protection de contenu et la diffusion de contenu en continu à la demande.</td>
    </tr>
    <tr>
        <td>Création de workflows hybrides</td>
        <td>Vous pouvez intégrer Media Services à des outils et processus existants. Par exemple, encodez un contenu local, puis téléchargez-le dans Media Services pour un transcodage en différents formats et une diffusion via le réseau de distribution de contenu (CDN) Azure ou via un CDN tiers. Media Services peut être appelé individuellement via des API REST standard pour une intégration à des applications et services externes.</td>
    </tr>
    <tr>
        <td>Prise en charge du cloud pour les lecteurs multimédias</td>
        <td>Vous pouvez créer, gérer et fournir des fichiers multimédias pour différents appareils (iOS, Android et Windows, par exemple) et plateformes.</td>
    </tr>
  </tbody>
</table>

<h3><a name="media-client"></a>Développement de clients Media Services</h3>
Élargissez la portée de votre solution Media Services en utilisant des Kits de développement logiciel (SDK) et des infrastructures de lecture afin de créer des clients multimédias. Ces clients sont destinés aux développeurs qui souhaitent créer des applications Media Services offrant des expériences utilisateur intéressantes sur un large éventail d'appareils et de plateformes. Selon les appareils ciblés, des options sont disponibles pour les Kits de développement logiciel (SDK) et les infrastructures de lecture de Microsoft et des partenaires tiers.  

Vous trouverez ci-dessous la liste des Kits de développement logiciel (SDK) et des infrastructures de lecture disponibles.  Pour plus d'informations sur les Kits de développement logiciel (SDK) et infrastructures de lecture ainsi que sur les fonctionnalités qu'ils prennent en charge, consultez la rubrique [Développement de clients Media Services][] sur les forums Media Services. 

####Prise en charge des clients Mac et PC  
Pour les PC et Mac, vous pouvez envisager une expérience de diffusion en continu à l'aide de Microsoft Silverlight ou d'Adobe Open Source Media Framework.

-	[Client de diffusion en continu lisse pour Silverlight](http://www.microsoft.com/fr-fr/download/details.aspx?id=29940)
-	[Plateforme multimédia Microsoft : Infrastructure de lecture pour Silverlight](http://smf.codeplex.com/documentation)
-	[Plug-in Smooth Streaming pour OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). Pour plus d'informations sur l'utilisation de ce plug-in, consultez la rubrique [Utilisation du plug-in Smooth Streaming pour Adobe Open Source Media Framework](../media-services-use-osmf-smooth-streaming-client-plugin/).

####Applications Windows 8
Pour Windows 8, vous pouvez créer des applications Windows Store en utilisant les langages et constructions de développement pris en charge, comme HTML, Javascript, XAML, C# et C+.

-	[Kit de développement logiciel (SDK) pour clients de diffusion en continu lisse Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). Pour plus d'informations sur la création d'une application Windows Store à l'aide de ce Kit de développement logiciel (SDK), consultez la rubrique [Création d'une application de diffusion en continu lisse Windows Store](http://go.microsoft.com/fwlink/?LinkId=271647). Pour plus d'informations sur la création d'un lecteur de diffusion en continu lisse en HTML5, consultez la rubrique [Procédure pas à pas : génération de votre premier lecteur de diffusion en continu lisse HTML5](http://msdn.microsoft.com/fr-fr/library/jj573656(v=vs.90).aspx).

-	[Plateforme multimédia Microsoft : Infrastructure de lecture pour applications Windows Store Windows 8](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
Xbox prend en charge les applications Xbox LIVE capables d'utiliser du contenu de diffusion en continu lisse. Le Kit de développement d'applications Xbox LIVE (ADK) comprend ce qui suit :

-	Client de diffusion en continu lisse pour ADK Xbox LIVE
-	Plateforme multimédia Microsoft : Infrastructure de lecture pour ADK Xbox LIVE

####Appareils intégrés ou dédiés
Appareils tels que les téléviseurs connectés, les décodeurs, les lecteurs Blu-Ray, les boîtiers TV OTT et les appareils mobiles dotés d'une infrastructure de développement d'applications personnalisée et d'un pipeline multimédia personnalisé. Microsoft fournit les kits de portage suivants, qui peuvent faire l'objet d'une licence, et permet aux partenaires de procéder au portage de la diffusion en continu lisse pour la plateforme.

-	[Kit de portage de client de diffusion en continu lisse](http://www.microsoft.com/fr-fr/mediaplatform/sspk.aspx)
-	[Kit de portage d'appareil Microsoft PlayReady](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
Microsoft fournit un Kit de développement logiciel (SDK) qui permet de créer des applications vidéo premium pour Windows Phone. 

-	[Client de diffusion en continu lisse pour Silverlight](http://www.microsoft.com/fr-fr/download/details.aspx?id=29940)
-	[Plateforme multimédia Microsoft : Infrastructure de lecture pour Silverlight](http://smf.codeplex.com/documentation)

####Appareils iOS
Pour les appareils iOS tels que les iPhone, iPod et iPad, Microsoft fournit un Kit de développement logiciel (SDK) grâce auquel vous pouvez créer des applications qui permettent à ces plateformes de fournir du contenu vidéo premium : Kit de développement logiciel (SDK) de diffusion en continu lisse pour appareils iOS avec PlayReady.  Ce Kit de développement logiciel (SDK) est uniquement accessible aux détenteurs de licences. Pour plus d'informations, veuillez adresser un [courrier électronique à Microsoft](mailto:askdrm@microsoft.com). Pour plus d'informations sur le développement iOS, consultez le [Centre de développement iOS](https://developer.apple.com/devcenter/ios/index.action).

####Appareils Android
Différents partenaires Microsoft fournissent des Kits de développement logiciel (SDK) pour la plateforme Android. Ceux-ci permettent de lire des fichiers de diffusion en continu lisse sur un appareil Android. Pour en savoir plus sur ces partenaires, veuillez adresser un [courrier électronique à Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices).

##Étapes suivantes
À présent que vous en savez un peu plus sur Media Services, consultez la rubrique [Configuration de votre ordinateur pour Media Services](../media-services-set-up-computer/) .


  [Présentation de Media Services]: #what-are
  [Développement de clients Media Services]: #media-client
  [Configuration d'un compte Azure pour Media Services]: #setup-account
  [Configuration pour le développement Media Services]: #setup-dev
  [  Connexion à Media Services par programme]: #connect
  [  Création d'un élément multimédia chiffré et chargement dans le stockage]: #create-asset
  [  Obtention d'une instance de processeur multimédia]: #get-mediaproc
  [  Vérification de la progression d'une tâche]: #check-job-progress
  [  Encodage d'un élément multimédia]: #encode-asset
  [  Protection d'un élément multimédia à l'aide de la protection PlayReady]: #playready
  [  Gestion des éléments multimédias dans le stockage]: #manage-asset
  [  Fourniture d'un élément multimédia par téléchargement]: #download-asset
  [  Fourniture de contenu de diffusion en continu]: #stream-asset
  [  Fourniture de contenu de diffusion en continu HLS Apple]: #stream-HLS
  [  Activation du CDN Azure]: #enable-cdn
  [Étapes suivantes]: #next-steps

  [Création d'applications avec l'API REST d'Azure Media Services]: http://go.microsoft.com/fwlink/?linkid=252967
  [Open Data Protocol]: http://odata.org/
  [WCF Data Services pour OData V3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Azure Marketplace]: https://datamarket.azure.com/
  [Développement de clients Media Services]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Version préliminaire de Media Services :  Fonctionnalités prises en charge]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/eb946433-16f2-4eac-834d-4057335233e0
  [Futures versions de Media Services :  Prise en charge de fonctionnalités planifiée]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/431ef036-0939-4784-a939-0ecb31151ded
  [Configuration d'un compte Media Services (version préliminaire)]: http://go.microsoft.com/fwlink/?linkid=247287
  [Kit de développement logiciel (SDK) Azure Media Services pour .NET]: http://go.microsoft.com/fwlink/?LinkID=256500
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Installation du Kit de développement logiciel (SDK) Azure sur Windows 8]: http://www.windowsazure.com/fr-fr/develop/net/other-resources/windows-azure-on-windows-8/
  [Documentation Azure Media Services]: http://go.microsoft.com/fwlink/?linkid=245437
  [Prise en main du CDN Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff919705.aspx
  [Media Services Forum]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?linkid=252966
  [Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Portail de gestion Azure]: https://manage.windowsazure.com/
  [Création d'un compte Media Services]: ../media-services-create-account/
  [Types de fichier pris en charge pour Media Services]: http://msdn.microsoft.com/fr-fr/library/dn535852.aspx



<!--HONumber=35.1-->
