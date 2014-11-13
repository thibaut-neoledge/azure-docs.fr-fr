<properties urlDisplayName="How to manage media content" pageTitle="Gestion du contenu multim&eacute;dia - Azure Media Services" metaKeywords="" description="Apprenez &agrave; g&eacute;rer votre contenu multim&eacute;dia dans Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="Gestion du contenu dans Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Gestion du contenu dans Media Services

La vue du contenu d'Azure Media Services vous permet de gérer le contenu multimédia de votre compte Media Services.

Vous pouvez actuellement effectuer les opérations suivantes à partir du portail :

-   afficher les informations de contenu telles que l'état de publication, l'URL publiée, la taille et la date/heure de la dernière mise à jour ;
-   télécharger un nouveau contenu ;
-   Encodage de contenu
-   lire un contenu vidéo ;
-   publier/annuler la publication d'un contenu ;
-   supprimer un contenu.

## Utilisation Téléchargement de contenu

1.  Sur le [portail de gestion][portail de gestion], cliquez sur **Media Services**, puis sur le nom du compte Media Services.
2.  Sélectionnez la page CONTENU.
3.  Cliquez sur le bouton **Télécharger** disponible sur cette page ou en bas du portail.
4.  Dans la boîte de dialogue **Télécharger le contenu**, accédez au fichier correspondant à l'élément multimédia de votre choix. Cliquez sur le fichier, puis sur **Ouvrir**, ou appuyez sur **Entrée**.

    ![UploadContentDialog][UploadContentDialog]

5.  Dans la boîte de dialogue Télécharger le contenu, cliquez sur le bouton de vérification pour accepter le nom du fichier et du contenu.
6.  Le téléchargement démarre et vous pouvez suivre la progression en bas du portail.

    ![JobStatus][JobStatus]

Une fois le téléchargement terminé, le nouvel élément multimédia est répertorié dans la liste Contenu. Par convention, la mention « **-Source** » est ajoutée à la fin du nom afin de faciliter le suivi des nouveaux contenus sources pour les tâches d'encodage.

Si la valeur relative à la taille du fichier n'est pas mise à jour après l'arrêt du processus de téléchargement, cliquez sur le bouton **Synchroniser les métadonnées**. La taille du fichier multimédia est alors synchronisée avec la taille réelle du fichier stocké et la valeur est actualisée sur la page Contenu.

## Utilisation Encodage de contenu

1.  Sur le [portail de gestion][portail de gestion], cliquez sur **Media Services**, puis sur le nom du compte Media Services.
2.  Cliquez sur la page CONTENU, en haut de la page.
3.  Cliquez sur la vidéo source de votre choix pour la tâche d'encodage, puis cliquez sur **Encoder** en bas de la page.
4.  Dans la boîte de dialogue Encodeur multimédia Azure, choisissez une des présélections d'encodage courantes ou avancées disponibles.

    **Présélections courantes**

    -   **Lecture sur PC/Mac (via Flash/Silverlight)**. Cette présélection produit un élément multimédia de diffusion en continu lisse doté des caractéristiques suivantes : CBR audio stéréo 44,1 kHz 16 bits/échantillon encodé à 96 Kb/s via AAC, et CBR vidéo 720p encodé selon 6 débits binaires allant de 3 400 Kb/s à 400 Kb/s via le profil principal H.264, et GOP de deux secondes.
    -   **Lecture via HTML5 (IE/Chrome/Safari)**. Cette présélection produit un fichier MP4 doté des caractéristiques suivantes : CBR audio stéréo 44,1 kHz 16 bits/échantillon encodé à 128 Kb/s via AAC, et CBR vidéo 720p encodé à 4 500 Kb/s via le profil principal H.264.
    -   **Lecture sur appareils iOS et PC/Mac**. Cette présélection produit un élément multimédia doté des mêmes caractéristiques que l'élément multimédia de diffusion en continu lisse (décrit ci-dessus), mais sous un format qui peut être utilisé pour une diffusion en continu HTTP Apple (HLS) aux appareils iOS.
    -   **Encodage avec la protection de contenu PlayReady**. Cette présélection produit un élément encodé avec la protection de contenu PlayReady. Pour que cette présélection puisse fonctionner, vous devez activer le service de distribution des licences PlayReady. À cet effet, accédez à l'onglet **PROTECTION DE CONTENU** et ajoutez une ligne à la table Branding Reporting. Le service de licences PlayReady de Media Services est activé quelques minutes après que vous avez appuyé sur ENREGISTRER.

        Le service de licences PlayReady de Media Services est utilisé par défaut. Si vous souhaitez spécifier un autre service à partir duquel les clients puissent obtenir une licence pour lire le contenu chiffré avec PlayReady, utilisez les API REST ou les API du Kit de développement logiciel (SDK) Media Services pour .NET. Pour plus d'informations, consultez la page [Utilisation du chiffrement statique pour protéger votre contenu][Utilisation du chiffrement statique pour protéger votre contenu] et définissez la propriété **licenseAcquisitionUrl** dans la présélection de Media Encryptor. Vous pouvez également utiliser le chiffrement dynamique et définir la propriété **PlayReadyLicenseAcquisitionUrl** comme décrit sur la page [Utilisation du chiffrement dynamique PlayReady et du service de distribution de licences][Utilisation du chiffrement dynamique PlayReady et du service de distribution de licences].

        Notez que cette option n'apparaîtra que si vous êtes inscrit à la version préliminaire de la fonctionnalité de protection de contenu PlayReady. Pour vous inscrire aux versions préliminaires, vous devez suivre la procédure décrite sur la page suivante : [Fonctionnalités préliminaires de Microsoft Azure][Fonctionnalités préliminaires de Microsoft Azure].

    **Présélections avancées**

    -   La rubrique [Chaînes de présélection des tâches pour l'Encodeur multimédia Azure][Chaînes de présélection des tâches pour l'Encodeur multimédia Azure] décrit les différentes présélections de la liste des présélections avancées.

    ![EncoderDialog][EncoderDialog]

    Actuellement, le portail ne prend pas en charge certains formats d'encodage pris en charge par l'Encodeur multimédia. De même, le chiffrement et le déchiffrement des éléments multimédias ne sont pas pris en charge. Vous pouvez exécuter ces tâches par programme. Pour plus d'informations, consultez les rubriques [Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET][Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET] et [Chaînes de présélection des tâches pour l'Encodeur multimédia Azure][Chaînes de présélection des tâches pour l'Encodeur multimédia Azure].

5.  Dans la boîte de dialogue Encodeur multimédia Azure, entrez un nom convivial pour le contenu de sortie ou acceptez le nom par défaut. Cliquez ensuite sur le bouton de vérification pour lancer l'opération d'encodage et suivez la progression en bas du portail.

    Une fois l'encodage terminé, votre vue doit être similaire à la capture d'écran ci-dessous.

    ![PortalViewUploadCompleted][PortalViewUploadCompleted]

    Si la valeur relative à la taille du fichier n'est pas mise à jour à l'issue de l'encodage, appuyez sur le bouton **Synchroniser les métadonnées**. La taille du fichier multimédia de sortie est alors synchronisée avec la taille réelle du fichier stocké et la valeur est actualisée sur la page Contenu.

## Utilisation Publication de contenu

1.  Sur le [portail de gestion][portail de gestion], cliquez sur **Media Services**, puis sur le nom du compte Media Services.
2.  Cliquez sur la page CONTENU, en haut de la page.
3.  Cliquez sur un élément multimédia qui n'est pas publié. Cliquez sur le bouton Publier pour publier une URL publique. Une fois le contenu publié dans une URL, cette dernière peut être ouverte par un lecteur client capable de lire le contenu encodé.

![PublishedContent][PublishedContent]

## Utilisation Lecture de contenu sur le portail

1.  Sur le [portail de gestion][portail de gestion], cliquez sur **Media Services**, puis sur le nom du compte Media Services.
2.  Cliquez sur la page CONTENU, en haut de la page.
3.  Cliquez sur le contenu vidéo de votre choix, puis sur le bouton **Lire** situé en bas du portail. Seuls les contenus qui ont été publiés peuvent être lus sur le portail. En outre, l'encodage doit être pris en charge par votre navigateur.

<!-- Images -->

  [portail de gestion]: http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409
  [UploadContentDialog]: ./media/media-services-manage-content/UploadContent.png
  [JobStatus]: ./media/media-services-manage-content/Status.png
  [Utilisation du chiffrement statique pour protéger votre contenu]: 
  [Utilisation du chiffrement dynamique PlayReady et du service de distribution de licences]: http://go.microsoft.com/fwlink/?LinkId=507720
  [Fonctionnalités préliminaires de Microsoft Azure]: http://azure.microsoft.com/fr-fr/services/preview/
  [Chaînes de présélection des tâches pour l'Encodeur multimédia Azure]: http://go.microsoft.com/fwlink/?LinkId=270865
  [EncoderDialog]: ./media/media-services-manage-content/EncoderDialog2.png
  [Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?LinkId=270866
  [PortalViewUploadCompleted]: ./media/media-services-manage-content/media-services-content-page.png
  [PublishedContent]: ./media/media-services-manage-content/media-services-upload-content-published.png
