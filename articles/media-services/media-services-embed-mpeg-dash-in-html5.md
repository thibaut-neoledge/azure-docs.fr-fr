<properties 
	pageTitle="Incorporation d’une vidéo de diffusion en continu adaptative MPEG-DASH dans une application HTML5 avec DASH.js | Microsoft Azure" 
	description="Cette rubrique montre comment incorporer une vidéo de diffusion en continu adaptative MPEG-DASH dans une application HTML5 avec DASH.js." 
	authors="Juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016" 
	ms.author="juliako"/>


#Incorporation d'une vidéo de diffusion en continu adaptative MPEG-DASH dans une application HTML5 avec DASH.js

##Vue d'ensemble

MPEG-DASH est une norme ISO pour la diffusion en continu adaptative de contenu vidéo, qui offre des avantages significatifs pour ceux qui souhaitent proposer un résultat de diffusion vidéo en continu adaptative de haute qualité. Avec MPEG-DASH, le flux vidéo est automatiquement ramené à une définition inférieure quand le réseau est encombré. Cela réduit le risque pour un utilisateur de voir une vidéo « interrompue » pendant que le lecteur télécharge les quelques secondes suivantes à lire (également appelée mise en mémoire tampon). À mesure que l'encombrement du réseau diminue, le lecteur vidéo renvoie à son tour un flux de qualité supérieure. Cette capacité d'adaptation de la bande passante requise entraîne également un temps de départ plus rapide pour la vidéo. Cela signifie que les premières secondes peuvent être lues dans un segment de moindre qualité rapide à télécharger, puis que la qualité s'améliore une fois le contenu suffisant mis en mémoire tampon.

Dash.js est un lecteur de vidéo MPEG-DASH open source écrit en JavaScript. Son objectif est de fournir un lecteur robuste, inter-plateformes qui peut être réutilisé librement dans les applications qui requièrent une lecture vidéo. Il assure la lecture MPEG-DASH dans n’importe quel navigateur prenant en charge W3C Media Source Extensions (MSE) aujourd’hui, à savoir Chrome, Microsoft Edge et IE11 (d’autres navigateurs ont indiqué leur intention de prendre en charge MSE). Pour plus d'informations sur DASH.js, consultez le référentiel dash.js GitHub.


##Création d'un lecteur vidéo de diffusion en continu basé sur le navigateur

Pour créer une page web simple qui affiche un lecteur vidéo avec les contrôles courants comme Lecture, Pause, Retour rapide, etc., vous devez effectuer les tâches suivantes :

1. Créer une page HTML
1. Ajouter la balise vidéo
1. Ajouter le lecteur dash.js
1. Initialiser le lecteur
1. Ajouter un style CSS
1. Afficher les résultats dans un navigateur qui implémente MSE

L'initialisation du lecteur peut être effectuée en seulement quelques lignes de code JavaScript. À l'aide de dash.js, il est vraiment très simple d'incorporer une vidéo MPEG-DASH dans vos applications basées sur le navigateur.

##Création de la page HTML

La première étape consiste à créer une page HTML standard qui contient l'élément <video>, à enregistrer ce fichier sous basicPlayer.html, comme l'illustre l'exemple suivant :
	
	<!DOCTYPE html>
	<html>
	  <head><title>Adaptive Streaming in HTML5</title></head>
	  <body>
	    <h1>Adaptive Streaming with HTML5</h1>
	    <video id="videoplayer" controls></video>
	  </body>
	</html>

##Ajout du lecteur DASH.js

Pour ajouter l'implémentation de référence dash.js à l'application, vous devez extraire le fichier dash.all.js de la version 1.0 du projet dash.js. Celui-ci doit être enregistré dans le dossier JavaScript de votre application. Ce fichier est un fichier de convenance qui rassemble tout le code dash.js requis dans un seul fichier. En examinant le contenu du référentiel dash.js, vous trouverez les fichiers individuels, le code de test, entre autres, mais si vous voulez seulement utiliser dash.js, alors c'est du fichier dash.all.js dont vous avez besoin.

Pour ajouter le lecteur dash.js à vos applications, ajoutez une balise de script à la section d'en-tête de basicPlayer.html :

	<!-- DASH-AVC/265 reference implementation -->
	< script src="js/dash.all.js"></script>


Ensuite, créez une fonction pour initialiser le lecteur pendant le chargement de la page. Ajoutez le script suivant après la ligne dans laquelle vous chargez dash.all.js :

	<script>
	// setup the video element and attach it to the Dash player
	function setupVideo() {
	  var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
	  var context = new Dash.di.DashContext();
	  var player = new MediaPlayer(context);
	                  player.startup();
	                  player.attachView(document.querySelector("#videoplayer"));
	                  player.attachSource(url);
	}
	</script>

Cette fonction crée d'abord un DashContext. Celui-ci permet de configurer l'application pour un environnement d'exécution spécifique. D'un point de vue technique, il définit les classes que l'infrastructure d'injection de dépendance doit utiliser pour construire l'application. Dans la plupart des cas, vous utiliserez Dash.di.DashContext.

Ensuite, instanciez la classe principale de l'infrastructure dash.js, MediaPlayer. Cette classe contient les principales méthodes requises telles que la lecture et la mise en pause, gère la relation avec l'élément vidéo et gère également l'interprétation du fichier MPD (Media Presentation Description) qui décrit la vidéo à lire.

La fonction startup() de la classe MediaPlayer est appelée pour s'assurer que le lecteur est prêt à lire la vidéo. Entre autres choses, cette fonction garantit que toutes les classes nécessaires (comme défini par le contexte) ont été chargées. Une fois que le lecteur est prêt, vous pouvez y associer l'élément vidéo à l'aide de la fonction attachView(). Cela permet à MediaPlayer d'injecter le flux vidéo dans l'élément et également de contrôler la lecture si besoin.

Passez l'URL du fichier MPD à MediaPlayer pour l'informer sur la vidéo à lire. La fonction setupVideo() tout juste créée devra être exécutée une fois la page entièrement chargée. Pour cela, utilisez l'événement onload de l'élément body. Remplacez votre élément <body> par :

	<body onload="setupVideo()">

Enfin, définissez la taille de l'élément vidéo à l'aide de CSS. Dans un environnement de diffusion en continu adaptative, cela s'avère particulièrement important car la taille de la vidéo lue peut changer au gré de l'adaptation de la lecture aux conditions changeantes du réseau. Cette démonstration simple force l'élément vidéo à constituer 80 % de la fenêtre de navigateur disponible en ajoutant le fichier CSS suivant à la section head de la page :
	
	<style>
	video {
	  width: 80%;
	  height: 80%;
	}
	</style>

##Lecture d'une vidéo

Pour lire une vidéo, pointez votre navigateur sur le fichier basicPlayback.html et cliquez sur Lire sur le lecteur vidéo affiché.


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Voir aussi

[Développement d'applications de lecteur vidéo](media-services-develop-video-players.md)

[Référentiel dash.js GitHub](https://github.com/Dash-Industry-Forum/dash.js)

<!---HONumber=AcomDC_0928_2016-->