
<properties 
    pageTitle="Nouveautés d’Azure RemoteApp"
    description="En savoir plus sur les modifications et les améliorations apportées à Azure RemoteApp" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# Nouveautés de RemoteApp

Un des avantages de RemoteApp est que nous nous efforçons de l’améliorer continuellement. Nous vous ferons part de toutes les améliorations sur cette page.


## Juin 2015

Que de changements ! L’équipe a été bien occupée en juin :

- La [page d’accueil](https://www.remoteapp.windowsazure.com/) Azure RemoteApp a été repensée, constatez-le vous-même ! 
- Le logiciel a été mis à jour dans toutes les images disponibles dans le cadre de votre abonnement.
- Des améliorations ont été apportées aux collections hybrides, dont la compatibilité avec le tunneling forcé et la vérification de la taille du sous-réseau IP avant d’essayer de créer la collection.
- Nous avons découvert que le caractère générique * ne fonctionne pas pour les webcams. À la place, vous devez spécifier l’ID d’instance ou le GUID. Nous mettrons à jour les informations de redirection pour refléter ces modifications.
- Vous pouvez désormais ajouter un logiciel antivirus personnalisé à votre image lorsque vous créez une image de modèle à partir de la galerie Azure.

Et d’autres modifications seront ajoutées en juillet. Nous reviendrons donc bientôt avec une autre mise à jour.

## Mai 2015

Il y a eu un certain nombre d’ajouts et quelques mois depuis la création de cette rubrique. Cette liste triche donc un peu et couvre la période allant de début mars à fin mai. Découvrez ces nouvelles fonctionnalités :

- Tout automatiser. Azure RemoteApp dispose désormais d’[applets de commande dans le module Azure PowerShell](remoteapp-tutorial-arawithpowershell.md). 
- [Création d’une image Azure RemoteApp à partir d’une machine virtuelle Azure](remoteapp-image-on-azurevm.md). Accélère le téléchargement de votre image personnalisée dans Azure.
- Utilisez un réseau virtuel Azure au lieu d’un réseau virtuel RemoteApp pour connecter les ressources de votre réseau d’entreprise à Azure. Nous avons mis à jour les [instructions sur les collections hybrides](remoteapp-create-hybrid-deployment.md) pour vous aider à créer un réseau virtuel Azure (Étape 1).
- Et toujours au sujet des réseaux virtuels, découvrez les [nouveaux conseils](remoteapp-vnetsizing.md) sur les limites de taille et les limitations des réseaux virtuels.
- Et en parlant de limites : quelles sont ces [limites de service et leurs valeurs par défaut](remoteapp-servicelimits.md) ?

Vous voulez en savoir plus sur Azure RemoteApp ? L’équipe RemoteApp était présente en force à la conférence Ignite il y a quelques semaines. Regardez la vidéo d’Eric, [Bases de la gestion et de l’administration de Microsoft Azure RemoteApp](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Vous avez besoin de voir Azure RemoteApp en action ? Découvrez le didacticiel [Exécutez n’importe quelle application sur n’importe quel appareil ](remoteapp-anyapp.md). Il vous montre comment partager Access avec vos utilisateurs, en partageant même les fichiers de base de données. Nous avons également un didacticiel sur comment [exécuter Office 365](remoteapp-tutorial-o365anywhere.md) de la même manière sur n’importe quel appareil.

Merci de nous rester fidèle. Nous serons de retour le mois prochain avec d’autres mises à jour.

<!---HONumber=August15_HO6-->