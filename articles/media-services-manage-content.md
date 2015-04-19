<properties 
	pageTitle="Gestion du contenu multimédia - Azure Media Services" 
	description="Apprenez à gérer votre contenu multimédia dans Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





# Gestion du contenu dans Media Services #

Cet article fait partie de la série [workflow à la demande de vidéo Media Services](media-services-video-on-demand-workflow.md). 

Cette rubrique présente l'utilisation du portail de gestion Azure pour gérer du contenu multimédia dans votre compte Media Services.

Vous pouvez actuellement effectuer les opérations suivantes à partir du portail :

- afficher les informations de contenu comme l'état publié, l'URL publiée, la taille, la date et l'heure de la dernière mise à jour, et si l'élément multimédia est chiffré ;
- télécharger un nouveau contenu ;
- indexer le contenu ;
- coder le contenu ;
- lire le contenu ;
- chiffrer ;
- publier/annuler la publication d'un contenu ;



##<a id="upload"></a>Procédure : de contenu 


1. Sur le [portail de gestion](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), cliquez sur **Media Services**, puis sur le nom du compte Media Services.
2. Sélectionnez la page CONTENU. 
3. Cliquez sur le bouton **Télécharger** disponible sur cette page ou en bas du portail. 
4. Dans la boîte de dialogue **Télécharger le contenu**, accédez au fichier correspondant à l'élément multimédia de votre choix. Cliquez sur le fichier, puis sur **Ouvrir** ou appuyez sur **Entrée**.

	![UploadContentDialog][uploadcontent]

5. Dans la boîte de dialogue Télécharger le contenu, cliquez sur le bouton de vérification pour accepter le nom du fichier et du contenu.
6. Le téléchargement démarre et vous pouvez suivre la progression en bas du portail.  

	![JobStatus][status]

Une fois le téléchargement terminé, le nouvel élément multimédia est répertorié dans la liste Contenu. Par convention, la mention " **-Source** " est ajoutée à la fin du nom afin de faciliter le suivi des nouveaux contenus sources pour les tâches d'encodage.

![ContentPage][contentpage]

Si la valeur relative à la taille du fichier n'est pas mise à jour après l'arrêt du processus de téléchargement, cliquez sur le bouton **Synchroniser les métadonnées**. La taille du fichier multimédia est alors synchronisée avec la taille réelle du fichier stocké et la valeur est actualisée sur la page Contenu.	

##<a id="index"></a>Procédure : Indexation du contenu

Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Vous pouvez indexer votre contenu à l'aide du portail de gestion en suivant les étapes présentées ci-dessous. Toutefois, si vous souhaitez davantage de contrôle sur les fichiers et sur la tâche d'indexation, vous pouvez utiliser le Kit de développement logiciel (SDK) Media Services pour .NET ou les API REST. Pour plus d'informations, consultez la page [Indexation des fichiers multimédias avec Azure Media Indexer](https://msdn.microsoft.com/fr-fr/library/azure/dn783455.aspx).

Les étapes qui suivent présentent comment utiliser le portail de gestion pour indexer votre contenu.

1. Sélectionnez le fichier que vous souhaitez indexer.
	Si l'indexation est prise en charge pour ce type de fichier, le bouton de traitement sera activé en bas de la page de contenu.
1. Appuyez sur le bouton de traitement.
2. Dans la boîte de dialogue de **traitement**, choisissez le processeur **Azure Media Indexer**.
3. Ensuite, complétez dans la boîte de dialogue de traitement le **titre** et la **description** du fichier multimédia d'entrée.
	
	![Process][process]

##<a id="encode"></a>Procédure : Encodage de contenu

Pour fournir une vidéo numérique sur Internet, vous devez compresser le contenu multimédia. Media Services propose un encodeur multimédia qui vous permet de spécifier comment vous souhaitez que votre contenu soit encodé (par exemple, le codec à utiliser, le format du fichier, la résolution et le débit). 

Lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Avec la diffusion à débit binaire adaptatif, le client peut basculer vers un flux à débit binaire supérieur ou inférieur, car la vidéo est affichée en fonction de la bande passante réseau actuelle, de l'utilisation de l'UC et d'autres facteurs. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement). 

Media Services fournit l'empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats. 

Pour tirer parti de l'empaquetage dynamique, vous devez effectuer les opérations suivantes :

- encoder votre fichier mezzanine (source) dans un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d'informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-manage-origins#scale_streaming_endpoints.md).

Avec l'empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients. 

Notez qu'en plus d'utiliser les fonctionnalités d'empaquetage dynamique, les unités réservées de diffusion à la demande vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbit/s. Par défaut, la diffusion en continu à la demande est configurée dans un modèle d'instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d'améliorer la vitesse de diffusion en continu à la demande, il est recommandé d'acheter des unités réservées de diffusion en continu à la demande.

Cette section décrit les étapes à suivre pour encoder votre contenu avec Azure Media Encoder à l'aide du portail de gestion.

1.  Sélectionnez le fichier que vous souhaitez encoder.
	Si l'encodage est pris en charge pour ce type de fichier, le bouton de traitement sera activé en bas de la page de contenu.
4. Dans la boîte de dialogue de **traitement**, choisissez le processeur **Azure Media Encoder**.
5. Choisissez une des **configurations d'encodage**.

	![Process2][process2]

		
	La rubrique [Chaînes de présélection des tâches pour Azure Media Encoder](https://msdn.microsoft.com/fr-fr/library/azure/dn619392.aspx) explique ce que signifie chaque présélection des catégories **présélections de diffusion adaptative (empaquetage dynamique)**, **présélections pour le téléchargement progressif**, **Présélections héritées pour la diffusion adaptative**  .  


	Les **Autres** configurations sont décrites ci-après :

	+ **Encodage avec la protection de contenu PlayReady**. Cette présélection produit un élément encodé avec la protection de contenu PlayReady.  
	
	
		Le service de licences PlayReady de Media Services est utilisé par défaut. Si vous souhaitez spécifier un autre service à partir duquel les clients puissent obtenir une licence pour lire le contenu chiffré avec PlayReady, utilisez les API REST ou les API du Kit de développement logiciel (SDK) Media Services pour .NET. Pour plus d'informations, consultez la page [Utilisation du chiffrement statique pour protéger votre contenu]() et définissez la propriété **licenseAcquisitionUrl** dans la présélection de Media Encryptor. Vous pouvez également utiliser le chiffrement dynamique et définir la propriété **PlayReadyLicenseAcquisitionUrl** comme décrit sur la page [Utilisation du chiffrement dynamique PlayReady et du service de distribution de licences](http://go.microsoft.com/fwlink/?LinkId=507720 ). 
	+ **Lecture sur PC/Mac (via Flash/Silverlight)**. Cette présélection produit un élément multimédia de diffusion en continu lisse doté des caractéristiques suivantes : CBR audio stéréo 44,1 kHz 16 bits/échantillon encodé à 96 Kb/s via AAC, et CBR vidéo 720p encodé selon 6 débits binaires allant de 3 400 Kb/s à 400 Kb/s via le profil principal H.264, et GOP de deux secondes.
	+ **Lecteur via HTML5 (IE/Chrome/Safari)**. Cette présélection produit un fichier MP4 doté des caractéristiques suivantes : CBR audio stéréo 44,1 kHz 16 bits/échantillon encodé à 128 Kb/s via AAC, et CBR vidéo 720p encodé à 4 500 Kb/s via le profil principal H.264.
	+ **Lecture sur appareils iOS et PC/Mac**. Cette présélection produit un élément multimédia doté des mêmes caractéristiques que l'élément multimédia de diffusion en continu lisse (décrit ci-dessus), mais sous un format qui peut être utilisé pour une diffusion en continu HTTP Apple (HLS) aux appareils iOS. 

5. Ensuite, entrez un nom convivial pour le contenu de sortie ou acceptez le nom par défaut. Cliquez ensuite sur le bouton de vérification pour lancer l'opération d'encodage et suivez la progression en bas du portail.
6. Appuyez sur OK.

	Une fois que l'encodage effectué, la page de contenu contient le fichier encodé. 

	Pour afficher la progression de la tâche d'encodage, basculez vers la page **travaux**.  


	Si la valeur relative à la taille du fichier n'est pas mise à jour à l'issue de l'encodage, appuyez sur le bouton **Synchroniser les métadonnées**. La taille du fichier multimédia de sortie est alors synchronisée avec la taille réelle du fichier stocké et la valeur est actualisée sur la page Contenu.	

##<a id="encrypt"></a>Procédure : Chiffrage du contenu

Si vous souhaitez que Media Services chiffre dynamiquement votre ressource avec une clé AES ou la DRM PlayReady, veillez à effectuer les opérations suivantes :

- encoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites dans la section [Encodage](#encode) ).
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d'informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-manage-origins#scale_streaming_endpoints.md).
- Configurez " stratégie de service de clé en clair aes par défaut " ou " stratégie de service de licence playready par défaut ". Pour plus d'informations, consultez [Configuration de la stratégie d'autorisation de clé de contenu](media-services-portal-configure-content-key-auth-policy.md).  


	Lorsque vous êtes prêt à activer le chiffrement, appuyez sur le bouton **CRYPTAGE** au bas de la page **CONTENU**.

	![Encrypt][encrypt] 

	Après avoir activé le chiffrement, lorsqu'un lecteur demande un flux de données, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l'aide du chiffrement AES ou PlayReady. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l'utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d'autorisation que vous avez spécifiées pour la clé.

##<a id="publish"></a>Procédure : Publication de contenu

Lorsque vous publiez le contenu, vous recevrez une URL de téléchargement progressif ou de diffusion en continu. Votre client pourra lire vos vidéos à l'aide de cette URL.

1. Cliquez sur l'élément multimédia que vous souhaitez publier. 
2. Puis, cliquez sur le bouton Publier. 
	
	Une fois le contenu publié dans une URL, cette dernière peut être ouverte par un lecteur client capable de lire le contenu encodé.

 ![PublishedContent][publishedcontent]

## Procédure : Lecture de contenu sur le portail

Le portail de gestion fournit un lecteur de contenu Media Services que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur le contenu vidéo de votre choix, puis sur le bouton **Lire** situé en bas du portail. 
 
Seuls les contenus qui ont été publiés peuvent être lus sur le portail. En outre, l'encodage doit être pris en charge par votre navigateur.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
<!--HONumber=45--> 
