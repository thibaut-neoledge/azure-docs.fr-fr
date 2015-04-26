<properties title="Publish an app in RemoteApp" pageTitle="Publication d'une application dans RemoteApp" description="Découvrez comment publier des applications et des ressources dans RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Publication d'une application dans RemoteApp

Après avoir créé votre collection cloud ou hybride, vous devez publier les applications ou les ressources que vous souhaitez rendre disponibles pour vos utilisateurs. Les images de modèle fournies avec votre abonnement ne contiennent que certaines applications publiées par défaut. Pour partager d'autres applications, vous devez les publier.
 
Sous l'onglet **Publication** du portail, cliquez sur **Publier**. Vous pouvez ajouter une application à partir du menu Démarrer de votre image de modèle ou indiquer le chemin d'accès de son répertoire d'installation dans l'image de modèle. Si vous choisissez la première option, sélectionnez dans la liste l'application à publier. Si vous choisissez d'indiquer le chemin d'accès à l'application, entrez un nom pour l'application et son chemin d'accès. Utilisez des variables dans le chemin d'accès, par exemple, " %lecteur_système% " au lieu de " c:\ ".

**Remarque :** si vous souhaitez ajouter votre application à partir du menu Démarrer, vous devez avoir *ajouté cette application au menu Démarrer dans votre image de modèle*. Sinon, comme RemoteApp n'affiche que les applications *figurant* dans le menu Démarrer, elle ne s'affichera pas. Si vous avez oublié d'ajouter l'application au menu Démarrer pendant la création du modèle, ajoutez le chemin d'accès à l'application (ou recréez votre image de modèle, mais cela demande plus de travail).

<!--HONumber=35.2-->
