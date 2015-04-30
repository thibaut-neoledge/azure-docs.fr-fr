<properties 
	pageTitle="Utilisation d'iOS Media Player Framework avec Azure Media Services" 
	description="Apprenez à utiliser la bibliothèque Media Services iOS Media Player Framework pour créer des applications riches et dynamiques." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>



# Utilisation d'Azure Media Services iOS Media Player Framework

La bibliothèque Azure Media Services iOS Media Player Framework simplifie la tâche des développeurs iPod, iPhone et iPad. Ils peuvent créer des applications clientes dynamiques et enrichies qui permettent de créer et de combiner instantanément des flux audio et vidéo. Par exemple, les applications qui affichent des contenus sportifs peuvent aisément insérer des publicités là où leur concepteur le souhaite et contrôler la fréquence d'affichage de ces publicités, même en cas de rembobinage du contenu principal. Les applications pédagogiques peuvent utiliser les mêmes fonctionnalités, par exemple, pour créer du contenu dans lequel les cours principaux affichent des commentaires ou des encadrés, avant de revenir au contenu principal.

D'ordinaire, la génération d'une application capable de créer des flux de contenu résultant d'une interaction entre l'application et son utilisateur est relativement complexe ; en temps normal, vous devez créer intégralement le flux et le stocker à l'avance sur le serveur. Grâce à iOS Media Player Framework, vous pouvez générer des applications clientes capables de faire tout cela sans contrôler ni modifier le flux de contenu principal. Vous pouvez :

- Planifier à l'avance les flux de contenu sur l'appareil client.
- Planifier des publicités ou des insertions pre-roll.
- Planifier des publicités ou des insertions post-roll.
- Planifier des publicités ou des insertions mid-roll et créer des pods publicitaires.
- Contrôler si la publicité ou l'insertion mid-roll se lance à chaque rembobinage de la chronologie du contenu ou si elle se lance une seule fois puis s'efface de la chronologie.
- Insérer dynamiquement du contenu directement dans la chronologie suite à un événement quelconque, que l'utilisateur ait appuyé sur un bouton ou que l'application ait reçu une notification d'un service. Par exemple, un programme de contenu d'actualités peut envoyer des notifications de dernières nouvelles et l'application peut mettre en pause le contenu principal pour charger dynamiquement un flux de dernières nouvelles. 

La combinaison de ces fonctionnalités avec la fonction lecteur multimédia des appareils iOS permet de créer des expériences multimédias très enrichies en très peu de temps avec moins de ressources.

Le SDK contient une application SamplePlayer qui montre comment créer une application iOS utilisant la plupart de ces fonctionnalités afin de créer instantanément un flux de contenu et permettre aux utilisateurs de déclencher une insertion de façon dynamique en appuyant sur un bouton. Ce didacticiel montre les principaux composants de l'application SamplePlayer et explique comment l'utiliser comme point de départ de votre application.

## Prise en main de l'application Sample Player
Procédez comme suit pour vous procurer l'application et obtenir une description des zones de l'application utilisant l'infrastructure. 

1. Clonez le référentiel git. 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. Ouvrez le projet situé dans `azure-media-player-framework/src/iOS/HLSClient/` : **SamplePlayer.xcodeproj**.

 
3. Voici la structure de l'exemple de lecteur :

![HLS Sample Code structure](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. Le dossier iPad contient deux fichiers .xib : **SeekbarViewController** et **SamplePlayerViewController**. Ils créent la disposition de l'interface utilisateur de l'application iPad. De même, deux fichiers .xib situés dans le dossier iPhone définissent la barre de recherche et le contrôleur. 

6. La logique principale de l'application se trouve dans **SamplePlayerViewController.m** dans le dossier `Shared`. La plupart des extraits de code décrits ci-dessous sont situés dans ce fichier. 

## Vue d'ensemble de la disposition de l'interface utilisateur
Deux fichiers .xib définissent notre interface lecteur. Dans ce qui suit, c'est la disposition de l'iPad qui est utilisée comme exemple, mais celle de l'iPhone lui ressemble beaucoup et les principes sont les mêmes.
### SamplePlayerViewController_iPad.xib
![Sample Player Address Bar](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* L'**URL du média** est l'URL utilisée pour charger un flux multimédia. L'application présente une liste préremplie d'URL de média que vous pouvez utiliser grâce aux boutons de sélection de l'URL. Vous pouvez également entrer votre propre URL de contenu de diffusion en continu HTTP (HLS). Ce contenu multimédia est utilisé comme premier contenu principal. 
**Remarque : veuillez ne pas laisser cette URL vide.**

* Les boutons **Sélection de l'URL** vous permettent de sélectionner des URL différentes de celles de la liste des URL de média.

### SeekbarViewController_iPad.xib
![Seek Bar Controller](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* Cliquez sur le bouton de **lecture** pour lire le contenu multimédia et faire une pause dans la lecture.

* La **Barre de recherche** présente l'ensemble de la chronologie de lecture. Pour faire une recherche, appuyez sur le bouton de recherche dans la barre de recherche, maintenez-le enfoncé en allant jusqu'au moment souhaité et relâchez-le. 

**Remarque** : lorsque l'utilisateur fait une recherche dans une publicité, une nouvelle barre de recherche apparaît et indique la durée de la publicité. La barre de recherche principale indique uniquement la durée du contenu principal ; ainsi, une publicité y affiche une durée de 0.

* La commande **Chronologie du lecteur** indique deux durées (`Label:playerTime`), par exemple, 00:23/02:10. Dans ce cas, 00:23 est la durée de lecture actuelle et 02:10 est la durée totale du média. 

* Les **boutons Avance rapide et Retour rapide** ne fonctionnent actuellement pas comme ils le devraient ; une version mise à jour sortira bientôt.

* En appuyant sur le bouton **Planifier maintenant** pendant la lecture du contenu principal, une publicité (dont vous pouvez définir l'URL source dans le code-behind) est insérée. Remarque : dans la version actuelle, vous ne pouvez pas planifier de publicité pendant qu'une autre est en lecture. 

### Planification du contenu principal
Planification d'un clip de contenu de 0 à 80 secondes :

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Dans l'exemple de code ci-dessus :

* L'objet **MediaTime** contrôle le clip vidéo que vous souhaitez planifier comme contenu principal. Dans l'exemple ci-dessus, le clip vidéo a une durée planifiée de 80 secondes (de 0 à 80 secondes).
* **clipBeginMediaTime** représente l'heure de début de lecture de la vidéo. Par exemple, si **clipBeginMediaTime** = 5, ce clip vidéo commencera 5 secondes après le début de la vidéo.
* **clipEndMediaTime** représente l'heure de fin de lecture de la vidéo. Si **clipEndMediaTime** = 100, ce clip vidéo finira à la 100e seconde de la vidéo.
*Ensuite, nous planifions **MediaTime** en demandant à l'infrastructure **appendContentClip**. Dans l'exemple ci-dessus, `[NSURL URLWithString:url]` donne l'URL du contenu principal et la planification de ce média est définie grâce à **withMedia** :
 `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])` .

**Remarque :**  planifiez toujours le contenu principal avant de planifier une publicité (y compris la publicité de pre-roll).

### Variation : si deux clips de contenu principal sont en train d'être lus, vous pouvez également planifier un deuxième clip après le premier grâce au code suivant :

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Ainsi, le code ci-dessus planifie deux flux de contenu sur la même chronologie de contenu. Le premier est planifié d'après `URLWithString:url` et le deuxième l'est d'après `URLWithString:secondContent`. Le deuxième contenu démarre à 30 secondes après le début du flux multimédia et finit à 80 secondes. 

## Planification d'une publicité 
Dans la version actuelle, seule une publicité **pauseTimeline=false** est prise en charge, ce qui signifie qu'à la fin d'une publicité, le lecteur reprendra là où le contenu principal s'était arrêté. 

Voici quelques points clés :
<ul><li> Toute la durée **LinearTime.duration** doit être sur 0 lors de la planification d'une publicité.</li>
<li> Lorsque **clipEndMediaTime** est plus long que la durée de la publicité, celle-ci se termine après et aucune exception n'est levée. Il vous est recommandé de vérifier si la durée naturelle de la publicité ne dépasse pas la durée de rendu (**clipEndMediaTime**) pour éviter de perdre une occasion de diffuser la publicité.</li> 
<li> Les publicités en pre-roll, mid-roll et post-roll sont prises en charge. Les pre-rolls peuvent uniquement être planifiés au tout début d'un contenu. Par exemple, vous ne pouvez pas planifier un pre-roll pour un deuxième contenu dans un scénario RCE (rough cut editing). </li>
<li> Les publicités permanentes et celles à affichage unique sont prises en charge et peuvent être utilisées avec les publicités en pre-roll, en mid-roll ou en post-roll.</li>
<li> Les publicités peuvent être au format .Mp4 ou HLS.</li>
</ul>
### Planification de publicités pre-roll, mid-roll, post-roll et de pods publicitaires

#### Planification de publicités pre-roll

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'objet **AdInfo** représente toutes les informations relatives à votre clip publicitaire :
* **ClipURL** est l'URL de la source du clip.
* La propriété **mediaTime** indique la durée d'apparition de la publicité. (**clipBeginMediaTime** indique l'heure de début et **clipEndMediaTime** l'heure de fin de la publicité.) Dans l'exemple de code ci-dessus, nous planifions une publicité de 5 secondes qui s'étend de la 0e à la 5e seconde de la durée de la publicité.
* L'objet **Policy** n'est pas actuellement utilisé par l'infrastructure.
* Vous devez définir la valeur **appendTo** sur -1 s'il ne s'agit pas d'un Ad Pod. 
* La valeur de **type** peut être pre-roll, mid-roll, post-roll ou ad pod. Pour pre-roll ou post-roll, spécifiez le type, car aucun minutage ne leur est associé. 

#### Planification de publicités mid-roll

Si vous ajoutez `adLinearTime.startTime = 23;` à l'exemple de code ci-dessus, la publicité se lancera à 23 secondes dans la chronologie du contenu principal.

#### Planification de publicités post-roll

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

La seule différence avec la planification de la publicité en pre-roll est `postAdInfo.type = AdType_Postroll;`. Le code ci-dessus planifie une publicité de 5 secondes comme post-roll. 

#### Planification de pods publicitaires
Un pod publicitaire est une pause publicitaire durant laquelle plusieurs publicités sont lues dos à dos. Voici le code pour planifier deux publicités dans un pod publicitaire. 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Plusieurs choses sont à remarquer ici :
* Pour le premier clip, **appendTo** est -1. Et lorsque nous appelons `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]`, `adIndex` reçoit une valeur unique indiquant la fin de ce premier clip du pod publicitaire. Ensuite, pour le deuxième clip du pod publicitaire, alignez le début de la deuxième publicité avec la fin de la première en définissant **appendTo** comme `adpodInfo2.appendTo = adIndex;`, ce qui précise que la position de fin du premier clip correspond à l'emplacement du début du deuxième. 
* Vous devez ensuite définir le type comme `AdType_Pod` pour indiquer qu'il s'agit d'un pod publicitaire. 

### Planification d'une publicité à lecture unique ou d'une publicité de type sticky
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

Comme l'illustre l'exemple de code ci-dessus, si vous définissez **deleteAfterPlay** sur **YES**, cette publicité sera seulement lue une fois. De plus, si vous définissez **deleteAfterPlay** sur **NO**, la lecture de cette publicité continue ; c'est ce qu'on appelle une " sticky ad ".
### Pour plus d'informations, consultez la rubrique [wiki de l'infrastructure Azure Media Player](https://github.com/WindowsAzure/azure-media-player-framework/wiki).

<!--HONumber=52-->