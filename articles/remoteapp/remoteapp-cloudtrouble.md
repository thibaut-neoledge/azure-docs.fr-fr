
<properties
    pageTitle="Résoudre les problèmes de création de collections cloud RemoteApp | Microsoft Azure"
    description="Apprenez comment dépanner les échecs de création de collections cloud RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="vikbucha" />



# Résolution des problèmes de création de collections cloud RemoteApp

Si vous rencontrez des difficultés à créer une collection cloud, consultez les informations suivantes.

## Votre image n'est pas valide ##
Si vous voyez un message similaire à « GoldImageInvalid » lorsque vous attendez qu'Azure configure votre collection, cela signifie que votre image de modèle ne répond pas aux [exigences définies pour l'image](remoteapp-imagereqs.md). Donc, lisez ces [exigences](remoteapp-imagereqs.md), corrigez votre image et essayez de créer à nouveau votre collection.

## Erreurs courantes dans le portail de gestion Azure

	DNS server could not be reached
	ProvisioningTimeout

La création de collections cloud échoue souvent si vous utilisez des images personnalisées. Si une des erreurs ci-dessus s’affiche et que vous utilisez une image personnalisée pour créer une collection, vérifiez les éléments suivants :

- Vérifiez que l’image personnalisée que vous avez téléchargée répond aux exigences relatives aux images.
- Le plus souvent, le problème est que l’image n’a pas été correctement préparée avec Sysprep.  
- Vérifiez que l’image peut démarrer dans Hyper-V ou essayez de créer une machine virtuelle IAAS à l’aide de l’image, directement dans votre abonnement Azure. Si la machine virtuelle ne parvient pas à démarrer et à se lancer, cela indique généralement que l’image personnalisée n’a pas été préparée correctement. Vérifiez que l’image personnalisée a été créée conformément à la procédure de Création d’une image de modèle personnalisée pour RemoteApp

Si vous utilisez l’une des images Microsoft incluses dans votre abonnement, essayez de créer la collection à nouveau. Si le problème persiste, contactez le support technique Microsoft.

	PlatformImageTrialModeOnly

Cette erreur signifie généralement que vous avez été mis à niveau vers un compte payant, mais que vous essayez d’utiliser une image fournie par Microsoft, valide uniquement pendant le mode d’évaluation du service. Dans ce cas, essayez de créer à nouveau votre collection cloud, en veillant à spécifier une image correcte.

<!---HONumber=AcomDC_1210_2015-->