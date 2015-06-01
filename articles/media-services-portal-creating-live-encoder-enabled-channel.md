<properties 
	pageTitle="Utiliser le portail de gestion pour créer des canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple" 
	description="Ce didacticiel vous guide tout au long des étapes de création d’un canal qui reçoit un flux dynamique à débit binaire unique et qui l’encode en flux à débit binaire multiple." 
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
	ms.date="04/29/2015" 
	ms.author="juliako"/>


#Utiliser le portail de gestion pour créer des canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple (version préliminaire)


Ce didacticiel vous guide tout au long des étapes de création d’un **canal** qui reçoit un flux continu à débit binaire unique et qui l’encode en flux à débit binaire multiple.

>[AZURE.NOTE]Pour plus d’informations sur les concepts relatifs aux canaux prenant en charge l’encodage en temps réel, voir [Utilisation de canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple](media-services-manage-live-encoder-enabled-channels.md).

##Scénario courant de diffusion dynamique en continu

Ci-après figurent les étapes générales impliquées dans la création d’applications courantes de diffusion en continu dynamique.

1. Connectez une caméra vidéo à un ordinateur. Lancez et configurez un encodeur dynamique local capable de générer un flux à débit binaire unique dans l’un des protocoles suivants : RTMP, Smooth Streaming ou RTP (MPEG-TS). Pour plus d’informations, voir [Prise en charge RTMP et encodeurs dynamiques dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Cette étape peut également être effectuée après la création du canal.

1. Créez et démarrez un canal.

1. Récupérez l’URL de réception du canal.

	L’URL de réception est utilisée par l’encodeur dynamique pour envoyer le flux au canal.
1. Récupérez l’URL d’aperçu du canal. 

	Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux dynamique.

3. Créez un programme (ce qui crée également une ressource).
1. Publiez le programme (ce qui crée un localisateur OnDemand pour la ressource associée).  

	Assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu.
1. Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l’archivage.
2. Un signal peut éventuellement être envoyé à l’encodeur dynamique pour qu’il démarre une publicité. La publicité est insérée dans le flux de sortie.
1. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l’événement.
1. Supprimez le programme (et éventuellement la ressource).   

##Dans ce didacticiel

Dans ce didacticiel, le portail de gestion Azure est utilisé pour effectuer les tâches suivantes :

2.  Configurez les points de terminaison de diffusion en continu.
3.  Créez un canal qui est activé pour effectuer un encodage en temps réel.
1.  Obtenez l’URL de réception afin de la fournir à l’encodeur dynamique. L’encodeur dynamique utilise cette URL pour recevoir le flux dans le canal.
1.  Créez un programme (et une ressource).
1.  Publiez la ressource et obtenez les URL de diffusion en continu.  
1.  Lecture de votre contenu 
2.  Nettoyage

##Configuration requise
Les éléments suivants sont requis pour suivre le didacticiel.

- Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](azure.microsoft.com).
- Un compte Media Services. Pour créer un compte Media Services, consultez [Créer un compte](media-services-create-account.md).
- Une webcam et un encodeur capable d’envoyer un flux dynamique à débit binaire unique.

##Configurer un point de terminaison de diffusion en continu à l’aide du portail

Lorsque vous utilisez Azure Media Services, l’un des scénarios les plus courants est la diffusion de contenu à débit binaire adaptatif à vos clients. Avec la diffusion à débit binaire adaptatif, le client peut basculer vers un flux à débit binaire supérieur ou inférieur, car la vidéo est affichée en fonction de la bande passante réseau actuelle, de l’utilisation de l’UC et d’autres facteurs. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Lorsque vous utilisez la diffusion en continu dynamique, un encodeur dynamique local (dans notre cas Wirecast) reçoit un flux dynamique à débit binaire multiple dans votre canal. Lorsque le flux est demandé par un utilisateur, Media Services utilise l’empaquetage dynamique pour empaqueter à nouveau le flux source dans le flux à débit binaire adaptatif demandé (TLS, DASH ou Smooth).

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

 
##Créer un CANAL

1.	Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur Media Services, puis sur le nom du compte Media Services.
2.	Sélectionnez la page CANAUX.
3.	Sélectionnez Ajouter+ pour ajouter un canal.

Choisissez les types d’encodage **Standard**. Ce type spécifie que vous souhaitez créer un canal qui est activé pour l’encodage en temps réel. Cela signifie que le flux entrant à débit binaire unique est envoyé au canal et encodé en un flux à débit binaire multiple à l’aide des paramètres d’encodeur dynamique spécifiés. Pour plus d’informations, voir [Utilisation de canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple](media-services-manage-live-encoder-enabled-channels.md).

![standard0][standard0]

Pour le type d’encodage **Standard**, les options de protocole de réception valides sont les suivantes :

- MP4 fragmenté (Smooth Streaming) à débit binaire unique
- RTMP à débit binaire unique
- RTP (MPEG-TS) : flux de transport MPEG-2 via RTP.

Pour obtenir une explication détaillée de chaque protocole, consultez [Utilisation de canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple](media-services-manage-live-encoder-enabled-channels.md).

![standard1][standard1]

Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée.

Dans la page **Configuration publicitaire**, vous pouvez spécifier la source pour les signaux des marqueurs de publicité. Lors de l’utilisation du portail, vous pouvez sélectionner uniquement API, qui indique que l’encodeur dynamique dans le canal doit écouter une API de marqueur de publicité asynchrone. Lorsque vous utilisez le portail, vous ne pouvez sélectionner qu’une API.

Pour plus d’informations, voir [Utilisation de canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple](media-services-manage-live-encoder-enabled-channels.md).

![standard2][standard2]

Dans la page **Présélection d’encodage**, vous pouvez sélectionner des préréglages système. Actuellement, le seul préréglage système que vous pouvez sélectionner est **Default720p**.

![standard3][standard3]

Dans la page **Création de canal**, vous pouvez définir les adresses IP autorisées à publier du contenu vidéo sur ce canal. Les adresses IP autorisées peuvent être spécifiées en tant qu’adresses IP uniques (par exemple, 10.0.0.1), une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau CIDR (par exemple, 10.0.0.1/22), ou une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, 10.0.0.1[255.255.252.0]).

Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP n’est autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.


![standard4][standard4]

>[AZURE.NOTE]Actuellement en version préliminaire, le démarrage du canal peut prendre jusqu’à 30 minutes. La réinitialisation du canal peut prendre jusqu’à 5 minutes.

Une fois que vous avez créé le canal, vous pouvez sélectionner l’onglet **ENCODEUR** où vous pouvez afficher les configurations de vos canaux. Vous pouvez également gérer les annonces et les ardoises.

![standard5][standard5]

Pour plus d’informations, voir [Utilisation de canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire multiple](media-services-manage-live-encoder-enabled-channels.md).


##Obtenir les URL de réception

Lorsque le canal est créé, vous pouvez obtenir des URL de réception que vous devez fournir à l’encodeur dynamique. L’encodeur utilise ces URL pour entrer un flux dynamique.

![readychannel](./media/media-services-managing-channels/media-services-ready-channel.png)


![ingesturls](./media/media-services-managing-channels/media-services-ingest-urls.png)


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

Une fois que le flux transite dans le canal, vous pouvez commencer l’événement de diffusion en continu en créant une ressource, un programme et un localisateur de diffusion en continu. Le flux est alors archivé et mis à la disposition des observateurs via le point de terminaison de diffusion en continu.

Il existe deux façons de démarrer un événement :

1. Dans la page **CANAL**, appuyez sur **AJOUTER** pour ajouter un programme.

	Spécifiez le nom du programme, le nom de la ressource, la fenêtre d’archivage et l’option de chiffrement.
	
	![createprogram](./media/media-services-managing-channels/media-services-create-program.png)
	
	Si vous avez laissé la case **Publier ce programme maintenant** cochée, le programme de publication des URL est créé.
	
	Vous pouvez appuyer sur **DÉMARRER** chaque fois que vous êtes prêt à diffuser le programme en continu.

	Une fois que vous avez démarré le programme, vous pouvez appuyer sur LIRE pour commencer à lire le contenu.


	![createdprogram](./media/media-services-managing-channels/media-services-created-program.png)

2. Vous pouvez également utiliser un raccourci et appuyer sur le bouton **DÉMARRER LA DIFFUSION EN CONTINU** dans la page **CANAL**. Une ressource, un programme et un localisateur de diffusion en continu sont alors créés.

	Le programme est nommé DefaultProgram, et la fenêtre d’archivage est définie sur 1 heure.

	Vous pouvez lire le programme publié dans la page CANAL.

	![channelpublish](./media/media-services-managing-channels/media-services-channel-play.png)


Si vous cliquez sur **ARRÊTER LA DIFFUSION EN CONTINU** dans la page **CANAL**, le programme par défaut est arrêté et supprimé. La ressource reste toujours disponible, et vous pouvez la publier ou en annuler la publication dans la page **CONTENU**.

Si vous basculez vers la page **CONTENU**, vous verrez les ressources qui ont été créées pour vos programmes.

![contentasset](./media/media-services-managing-channels/media-services-content-assets.png)


##Lecture de contenu

Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser votre contenu, vous devez d’abord « publier » votre ressource (comme indiqué dans la section précédente) en créant un localisateur (lorsque vous publiez une ressource à l’aide du portail, des localisateurs sont créés pour vous). Les localisateurs assurent l’accès aux fichiers contenus dans l’élément multimédia.

En fonction du protocole de diffusion en continu à utiliser pour lire votre contenu, vous devrez peut-être modifier l’URL que vous obtenez à partir du lien **URL DE PUBLICATION** du canal/programme.

L’empaquetage dynamique correspond à l’empaquetage du flux dynamique dans le protocole spécifié.

Par défaut, les URL de diffusion en continu que vous pouvez utiliser pour lire les ressources Smooth Streaming, ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Pour créer une URL de diffusion en continu HLS, ajoutez (format=m3u8-aapl) à l’URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Pour créer une URL de diffusion en continu MPEG DASH, ajoutez (format=mpd-time-csf) à l’URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Pour plus d’informations sur la transmission de votre contenu, voir [Distribution de contenu](media-services-deliver-content-overview.md).

Vous pouvez lire un flux Smooth Stream à l’aide du [Lecteur AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) ou utiliser des appareils iOS et Android pour lire HLS version 3.

##Nettoyer

Si vous avez terminé de diffuser en continu les événements et que vous voulez nettoyer les ressources configurées précédemment, suivez la procédure ci-dessous.

- Arrêtez d’envoyer le flux à partir de l’encodeur.
- Arrêtez le canal. Une fois le canal arrêté, aucun frais n’est encouru. Lorsque vous devez le redémarrer, il possède la même URL de réception. Vous n’avez donc pas besoin de reconfigurer votre encodeur.
- Vous pouvez arrêter votre point de terminaison de diffusion en continu, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct comme un flux à la demande. Si le canal est arrêté, aucun frais n’est encouru.
  


[standard0]: ./media/media-services-managing-channels/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-managing-channels/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-managing-channels/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-managing-channels/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-managing-channels/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-managing-channels/media-services-create-channel-standard_encode.png
<!--HONumber=52-->
