
<properties 
    pageTitle="Téléchargement d'une image personnalisée"
    description="Découvrez comment télécharger une image personnalisée vers RemoteApp." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="ericor" />



# Téléchargement d'une image personnalisée

Maintenant que vous avez créé votre image de modèle personnalisée ou l'avez mise à jour avec des modifications, vous devez télécharger cette image dans votre bibliothèque d'images Azure RemoteApp. Suivez ces étapes. 


## Avant de commencer

1.      Vérifiez que votre image personnalisée est conforme aux [exigences relatives aux images](..\remoteapp-imagereqs)et aux [exigences relatives aux applications](..\remoteapp-appreqs).
2.      Installez le module [Azure PowerShell](../install-configure-powershell.md).

## Guide étape par étape pour le téléchargement d'une image personnalisée

1.      Ouvrez le portail de gestion Azure et accédez à la page RemoteApp.
2.      Dans l'onglet **Images de modèle**, cliquez sur **Télécharger** en bas de la page.
4.      Entrez un nom convivial pour votre image et spécifiez l'emplacement du compte de stockage. Vérifiez que l'emplacement est le même que votre collection RemoteApp ou est un emplacement où vous souhaitez en créer une. 
5.      Lorsque vous y êtes invité, téléchargez le script sur votre ordinateur local.
6.      Copiez les paramètres de commande de la zone de texte dans votre Presse-papiers.
7.      Ouvrez une fenêtre Windows PowerShell avec élévation de privilèges  
8.      Dans la fenêtre Windows PowerShell avec élévation de privilèges, accédez au répertoire où vous avez téléchargé le script.
9.      Collez la commande copiée et appuyez sur **Entrée**.

	Le téléchargement commence. Sa durée dépend de nombreux facteurs, notamment la vitesse de votre réseau et la taille de l'image

11.    Si votre téléchargement échoue en raison d'une interruption du réseau ou d'un problème similaire, vous pouvez toujours reprendre le processus de téléchargement commencé. Pour reprendre un téléchargement, exécutez le script en utilisant la même ligne de commande.

**Avertissement :** ne modifiez jamais le script de téléchargement. Des vérifications spécifiques ont été implémentées pour vous assurer que l'image répond aux exigences relatives aux images et aux applications.

## Problèmes courants

- Veillez à utiliser Windows PowerShell, et non Azure PowerShell.  Vous devez installer le module Azure PowerShell, car certains modules sont nécessaires pour le processus de téléchargement. 
- Ne modifiez jamais le script, les validations sont pensées pour vous faciliter la tâche.
- Si le fichier de disque dur virtuel est verrouillé pendant le téléchargement, copiez le fichier ou déplacez-le vers un nouvel emplacement et essayez à nouveau. Un processus Windows peut empêcher le téléchargement.  


<!--HONumber=52--> 