<properties 
	pageTitle="Utiliser le portail de gestion pour créer des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux" 
	description="Ce didacticiel vous guide dans les étapes d’implémentation d’une application de diffusion en continu dynamique Media Services de base où un canal reçoit un flux dynamique à débit binaire multiple provenant d’un encodeur dynamique local." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="juliako"/>


#Utiliser le portail de gestion pour créer des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]


Ce didacticiel vous guide dans les étapes d’implémentation d’une application de diffusion en continu dynamique Media Services de base où un **canal** reçoit un flux dynamique à débit binaire multiple provenant d’un encodeur dynamique local. Pour une vue d’ensemble plus détaillée de l’utilisation des canaux et des composants associés, consultez [Utilisation des canaux qui reçoivent un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-channels-overview.md).

Dans ce didacticiel, le portail de gestion Azure est utilisé pour effectuer les tâches suivantes :

2.  Configurer les points de terminaison de diffusion en continu
3.  Créer un canal
1.  Configurer un encodeur dynamique et recevoir un flux dynamique dans le canal \(Wirecast est utilisé à cette étape\)
1.  Créer un programme \(et une ressource\)
1.  Publier la ressource et obtenir les URL de diffusion en continu  
1.  Lecture de votre contenu 
2.  Nettoyage

##Configuration requise
Les éléments suivants sont requis pour suivre le didacticiel.

- Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](azure.microsoft.com).
- Un compte Media Services. Pour créer un compte Media Services, consultez [Créer un compte](media-services-create-account.md).
- Une webcam et un encodeur capable d’envoyer un flux dynamique à débit binaire multiple.

	 
##Configurer un point de terminaison de diffusion en continu à l’aide du portail

Lorsque vous utilisez Azure Media Services, l’un des scénarios les plus courants est la diffusion de contenu en continu à débit binaire adaptatif à vos clients. Avec la diffusion à débit binaire adaptatif, le client peut basculer vers un flux à débit binaire supérieur ou inférieur, car la vidéo est affichée en fonction de la bande passante réseau actuelle, de l’utilisation de l’UC et d’autres facteurs. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming \(HLS\), Smooth Streaming, MPEG DASH et HDS \(pour licences Adobe PrimeTime/Access uniquement\).

Lorsque vous utilisez la diffusion en continu dynamique, un encodeur dynamique local \(dans notre cas Wirecast\) reçoit un flux dynamique à débit binaire multiple dans votre canal. Lorsque le flux est demandé par un utilisateur, Media Services utilise l’empaquetage dynamique pour empaqueter à nouveau le flux source dans le flux à débit binaire adaptatif demandé \(TLS, DASH ou Smooth\).

Pour tirer parti de l’empaquetage dynamique, vous devez obtenir au moins une unité de diffusion en continu pour le **point de terminaison de diffusion en continu** à partir duquel vous envisagez de distribuer votre contenu.

Pour changer le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page POINTS DE TERMINAISON DE DIFFUSION EN CONTINU. Cliquez ensuite sur le point de terminaison de diffusion en continu que vous souhaitez modifier.

3. Pour spécifier le nombre d'unités de diffusion en continu, sélectionnez l'onglet METTRE À L'ÉCHELLE et déplacez le curseur de **capacité réservée**.

	![Page Mettre à l'échelle](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	L’allocation de nouvelles unités prend environ 20 minutes.

	 
	>[AZURE.NOTE]Actuellement, le fait de passer d’une valeur positive à zéro pour le nombre d’unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.
	>
	> C’est le plus grand nombre d’unités spécifiées sur 24 heures qui est utilisé pour calculer le coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification des services de média](http://go.microsoft.com/fwlink/?LinkId=275107).


##Créer un canal

Dans le portail de gestion Azure, sélectionnez la page **CANAL**, puis cliquez sur **NOUVEAU**. Dans la boîte de dialogue **Créer un canal en direct**, entrez le nom de votre canal.

![createchannel](./media/media-services-managing-channels/media-services-create-channel.png)

Appuyez sur **OK**.

Après quelques minutes, le canal est créé et démarré.

##Obtenir les URL de réception

Lorsque le canal est créé, vous pouvez obtenir des URL de réception que vous devez fournir à l’encodeur dynamique. L’encodeur utilise ces URL pour entrer un flux dynamique.

![readychannel](./media/media-services-managing-channels/media-services-ready-channel.png)


![ingesturls](./media/media-services-managing-channels/media-services-ingest-urls.png)

Pour plus d’informations sur les URL de réception, voir [Utilisation d’encodeurs locaux pour envoyer un flux dynamique à débit binaire multiple vers un canal](media-services-channels-overview.md).

##Configurer un encodeur dynamique et recevoir un flux dynamique 

>[AZURE.NOTE]Cette étape nécessite l’URL de réception du canal qui a été indiquée à l’étape précédente.

Pour plus d’informations sur la configuration de Wirecast et le démarrage de la réception du flux, voir [Configuration de Wirecast](http://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/).

>[AZURE.NOTE]Si pour une raison quelconque, vous arrêtez l’encodeur, puis devez le redémarrer, vous devez tout d’abord réinitialiser le canal en cliquant sur la commande **RESET** dans le portail de gestion Azure.


##Créer et gérer un programme

###Vue d'ensemble

Un canal est associé à des programmes vous permettant de contrôler la publication et le stockage des segments dans un flux dynamique. Les canaux gèrent des programmes. La relation entre canal et programme est très similaire au contenu multimédia traditionnel où un canal a un flux de contenu constant et un programme est limité à un événement minuté sur ce canal.

Vous pouvez spécifier le nombre d’heures pendant lesquelles vous souhaitez conserver le contenu enregistré pour le programme en définissant la durée de la **fenêtre d’archivage**. Cette valeur peut être comprise entre 5 minutes et 25 heures. La durée de la fenêtre d’archivage détermine également la plage maximale de temps dans laquelle les clients peuvent effectuer des recherches en arrière à partir de la position dynamique actuelle. Les programmes peuvent durer davantage que le laps de temps spécifié, mais le contenu qui se situe en dehors de la longueur de fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la longueur maximale que les manifestes de client peuvent atteindre.

Chaque programme est associé à un élément multimédia. Pour publier le programme, vous devez créer un localisateur OnDemand pour l’élément multimédia associé. Le fait de posséder ce localisateur vous permettra de générer une URL de diffusion en continu que vous pourrez fournir à vos clients.

Un canal prend en charge jusqu’à trois programmes exécutés simultanément, ce qui rend possible la création de plusieurs archives du même flux entrant. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Par exemple, imaginez que vous devez archiver 6 heures d’un programme, mais diffuser uniquement les 10 dernières minutes. Pour ce faire, vous devez créer deux programmes exécutés simultanément. Un programme est configuré pour archiver 6 heures de l’événement, mais il n’est pas publié. L’autre programme est configuré pour archiver pendant 10 minutes et il est publié.

Vous ne devez pas réutiliser de programmes existants pour de nouveaux événements. Au lieu de cela, créez et démarrez un nouveau programme pour chaque événement, tel que décrit dans la section Programmation d’applications de vidéo en flux continu.

Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l’archivage. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l’événement.

Pour supprimer du contenu archivé, arrêtez et supprimez le programme, puis supprimez l’élément multimédia associé. Un élément multimédia ne peut pas être supprimé s’il est utilisé par un programme ; le programme doit d’abord être supprimé.

Même après l’arrêt et la suppression du programme, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia.

Si vous souhaitez conserver le contenu archivé sans qu’il soit disponible pour la diffusion, supprimez le localisateur de diffusion en continu.

###Créer/Démarrer/Arrêter des programmes

Une fois que le flux transite dans le canal, vous pouvez commencer l’événement de diffusion en continu en créant une ressource, un programme et un localisateur de diffusion en continu. Cela archive le flux et le met à la disposition des observateurs via le point de terminaison de diffusion en continu.

Il existe deux façons de démarrer un événement :

1. Dans la page **CANAL**, appuyez sur **AJOUTER** pour ajouter un programme.

	Spécifiez le nom du programme, le nom de la ressource, la fenêtre d’archivage et l’option de chiffrement.
	
	![createprogram](./media/media-services-managing-channels/media-services-create-program.png)
	
	Si vous avez laissé la case **Publier ce programme maintenant** cochée, le programme de publication des URL est créé.
	
	Vous pouvez appuyer sur **DÉMARRER** chaque fois que vous êtes prêt à diffuser le programme en continu.

	Une fois que vous avez démarré le programme, vous pouvez appuyer sur LIRE pour commencer à lire le contenu.


	![createdprogram](./media/media-services-managing-channels/media-services-created-program.png)

2. Vous pouvez également utiliser un raccourci et appuyer sur le bouton **DÉMARRER LA DIFFUSION EN CONTINU** dans la page **CANAL**. Cela crée une ressource, un programme et un localisateur de diffusion en continu.

	Le programme est nommé DefaultProgram, et la fenêtre d’archivage est définie sur 1 heure.

	Vous pouvez lire le programme publié dans la page CANAL.

	![channelpublish](./media/media-services-managing-channels/media-services-channel-play.png)


Si vous cliquez sur **ARRÊTER LA DIFFUSION EN CONTINU** dans la page **CANAL**, le programme par défaut est arrêté et supprimé. La ressource reste toujours disponible, et vous pouvez la publier ou en annuler la publication dans la page **CONTENU**.

Si vous basculez vers la page **CONTENU**, vous verrez les ressources qui ont été créées pour vos programmes.

![contentasset](./media/media-services-managing-channels/media-services-content-assets.png)


##Lecture de contenu

Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser votre contenu en continu, vous devez d’abord « publier » votre ressource \(comme indiqué dans la section précédente\) en créant un localisateur \(lorsque vous publiez une ressource à l’aide du portail, des localisateurs sont créés pour vous\). Les localisateurs assurent l’accès aux fichiers contenus dans l’élément multimédia.

En fonction du protocole de diffusion en continu à utiliser pour lire votre contenu, vous devrez peut-être modifier l’URL que vous obtenez à partir du lien **URL DE PUBLICATION** du canal/programme.

L’empaquetage dynamique correspond à l’empaquetage du flux dynamique dans le protocole spécifié.

Par défaut, les URL de diffusion en continu que vous pouvez utiliser pour lire les ressources Smooth Streaming, ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Pour créer une URL de diffusion en continu HLS, ajoutez \(format=m3u8-aapl\) à l’URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Pour créer une URL de diffusion en continu MPEG DASH, ajoutez \(format=mpd-time-csf\) à l’URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Pour plus d’informations sur la transmission de votre contenu, voir [Distribution de contenu](media-services-deliver-content-overview.md).

Vous pouvez lire un flux Smooth Stream à l’aide du [Lecteur AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) ou utiliser des appareils iOS et Android pour lire HLS version 3.

##Nettoyer

Si vous avez terminé de diffuser en continu les événements et que vous voulez nettoyer les ressources configurées précédemment, suivez la procédure ci-dessous.

- Arrêtez d’envoyer le flux à partir de l’encodeur.
- Arrêtez le canal. Une fois le canal arrêté, aucun frais n’est encouru. Lorsque vous devez le redémarrer, il possède la même URL de réception. Vous n’avez donc pas besoin de reconfigurer votre encodeur.
- Vous pouvez arrêter votre point de terminaison de diffusion en continu, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct en tant que flux à la demande. Si le canal est arrêté, aucun frais n’est encouru.



##Étape suivante

En savoir plus sur la création d’applications de diffusion en continu dynamique [Créer des applications de diffusion en continu dynamique](media-services-live-streaming-workflow.md)

###Ressources supplémentaires
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Mettez votre vidéo en ligne dès maintenant.</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Empaquetage dynamique et appareils mobiles</a>


<!-- Anchors. -->


<!-- URLs. -->
[Management Portal]: http://manage.windowsazure.com/

<!-- Images -->

<!--HONumber=52-->
