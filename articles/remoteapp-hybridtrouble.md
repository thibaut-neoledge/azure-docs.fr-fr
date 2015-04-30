
<properties 
    pageTitle="Dépannage des collections hybrides : création"
    description="Apprenez comment dépanner les échecs de création de collections hybrides RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# Dépannage des collections hybrides : création

Avant de pouvoir résoudre les échecs lors de la création de collections hybrides, vous devez comprendre comment elles sont créées. Une collection hybride nécessite que les instances RemoteApp soient jointes au domaine : vous effectuez cela lors de la création de la collection.  Au démarrage du processus de création de collection, des copies des images de modèle que vous avez téléchargées sont créées dans le réseau virtuel et sont jointes, via le tunnel VPN de site à site, au domaine qui est résolu par l'enregistrement IP DNS spécifié lors de la création du réseau virtuel.

Erreurs courantes dans le portail de gestion Azure :

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

Si l'une des erreurs ci-dessus s'affiche, vérifiez les éléments suivants :

- Vérifiez que les configurations IP DNS sont valides
- Assurez-vous que les enregistrements IP DNS sont des enregistrements IP publics ou qu'ils font partie de " l'espace de l'adresse locale " que vous avez spécifié lors de la création du réseau virtuel
- Vérifiez le tunnel de réseau virtuel pour vous assurer que son état est actif ou connecté 
- Assurez-vous que le côté local de la connexion VPN ne bloque pas le trafic réseau. Vous pouvez vérifier cela en examinant les journaux de votre périphérique ou logiciel VPN local.
- Assurez-vous que le domaine que vous avez spécifié lors de la création de la collection est en cours d'exécution.

	ProvisioningTimeout


Si cette erreur s'affiche, vérifiez les éléments suivants :

- Assurez-vous que le côté local de la connexion VPN ne bloque pas le trafic réseau. Vous pouvez vérifier cela en examinant les journaux de votre périphérique ou logiciel VPN local.
- Vérifiez que l'image de modèle RemoteApp que vous avez téléchargée a été correctement préparée avec Sysprep. Vous pouvez vérifier les exigences relatives aux images RemoteApp ici: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- Essayez de créer une machine virtuelle à l'aide de l'image de modèle que vous avez téléchargée et assurez-vous qu'elle démarre et s'exécute correctement soit (a) sur un serveur Hyper-V local, soit (b) par la création d'une machine virtuelle IAAS Azure dans votre abonnement Azure. Si la création ou le démarrage de la machine virtuelle échoue, c'est généralement signe que l'image de modèle n'a pas été préparée correctement et que vous devez résoudre le problème.

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

Si l'une des erreurs ci-dessus s'affiche, vérifiez les éléments suivants :

- Vérifiez que les informations d'identification pour la jonction de domaine sont valides
- Vérifiez que les informations d'identification de jonction de domaine sont correctes et disposent des autorisations de jonction de domaine appropriées
- Vérifiez que l'unité d'organisation est correctement formatée et qu'elle n'existe pas dans Active Directory.

<!--HONumber=52-->