
<properties 
    pageTitle="Résolution des problèmes de collections cloud RemoteApp : création"
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
    ms.date="08/12/2015" 
    ms.author="vikbucha" />



# Résolution des problèmes de création de collections cloud RemoteApp

Erreurs courantes dans le portail de gestion Azure :

	DNS server could not be reached
	ProvisioningTimeout

La création de collections cloud échoue souvent si vous utilisez des images personnalisées. Si une des erreurs ci-dessus s’affiche et que vous utilisez une image personnalisée pour créer une collection, vérifiez les éléments suivants :

- Vérifiez que l’image personnalisée que vous avez téléchargée répond aux exigences relatives aux images. 
- Le plus souvent, le problème est que l’image n’a pas été correctement préparée avec Sysprep.  
- Vérifiez que l’image peut démarrer dans Hyper-V ou essayez de créer une machine virtuelle IAAS à l’aide de l’image, directement dans votre abonnement Azure. Si la machine virtuelle ne parvient pas à démarrer et à se lancer, cela indique généralement que l’image personnalisée n’a pas été préparée correctement. Vérifiez que l’image personnalisée a été créée conformément à la procédure de Création d’une image de modèle personnalisée pour RemoteApp

Si vous utilisez l’une des images Microsoft incluses dans votre abonnement, essayez de créer la collection à nouveau. Si le problème persiste, contactez le support technique Microsoft.

	PlatformImageTrialModeOnly

Cette erreur signifie généralement que vous avez été mis à niveau vers un compte payant, mais que vous essayez d’utiliser une image fournie par Microsoft, valide uniquement pendant le mode d’évaluation du service. Dans ce cas, essayez de créer à nouveau votre collection cloud, en veillant à spécifier une image correcte.
 

<!---HONumber=August15_HO7-->