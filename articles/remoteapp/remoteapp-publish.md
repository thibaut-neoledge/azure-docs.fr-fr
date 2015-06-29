<properties
    pageTitle="Publication d'une application dans RemoteApp"
    description="Découvrez comment publier des applications et des ressources dans RemoteApp."
    services="remoteapp"
    solutions="" documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2015"
    ms.author="elizapo" />


# Publication d'une application dans RemoteApp

Après avoir créé votre collection RemoteApp, vous devez publier les applications ou les ressources que vous souhaitez rendre disponibles pour vos utilisateurs. Les images de modèle fournies avec votre abonnement ne contiennent que certaines applications publiées par défaut. Pour partager d'autres applications, vous devez les publier.

> [AZURE.NOTE]Vous devez mettre à jour une application ? Il vous faut d'abord [mettre à jour l'image](remoteapp-update.md).

Sous l'onglet **Publication ** du portail, cliquez sur **Publier**. Vous pouvez ajouter une application à partir du menu **Démarrer** de votre image de modèle ou indiquer le chemin d'accès de son répertoire d'installation dans l'image de modèle. Si vous choisissez la première option, sélectionnez dans la liste l'application à publier. Si vous choisissez d'indiquer le chemin d'accès à l'application, entrez un nom pour l'application et son chemin d'accès. Utilisez des variables dans le chemin d'accès, par exemple, « %lecteur_système% » au lieu de « c: ».

> [AZURE.NOTE]Si vous voulez ajouter votre application à partir du menu Démarrer, vous devez avoir *ajouté cette application au menu Démarrer sur votre image de modèle.* Sinon, RemoteApp ne voit que ce qui *figure* dans le menu Démarrer et cela peut prêter à confusion. Si vous avez oublié d'ajouter l'application au menu **Démarrer** au moment de la création du modèle, choisissez d'ajouter le chemin d'accès à l'application. (Ou recréez votre image de modèle, mais c'est un peu plus de travail.)
 

<!---HONumber=58_postMigration-->