---
title: Comment créer une collection cloud d’Azure RemoteApp | Microsoft Docs
description: Découvrez comment créer un déploiement d'Azure RemoteApp qui enregistre les données dans le cloud Azure.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin
editor: ''

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Création d'une collection cloud d’Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Il existe deux types de [collections Azure RemoteApp](remoteapp-collections.md) :

* Cloud : réside complètement dans Azure. Vous pouvez soit enregistrer toutes les données dans le cloud (la collection se trouve uniquement dans le cloud), soit connecter votre collection à un réseau virtuel et enregistrer vos données dans celui-ci.
* Hybride : inclut un réseau virtuel pour l’accès local (nécessite Azure AD et un environnement Active Directory local).

Ce didacticiel vous guide dans la procédure de création d'une collection cloud. Il se compose de quatre étapes :

1. Créez une collection Azure RemoteApp.
2. Vous pouvez éventuellement configurer une synchronisation d'annuaires. Si vous utilisez Azure AD et Active Directory, vous devez synchroniser les utilisateurs, contacts et mots de passe de votre Active Directory local avec votre locataire Azure AD.
3. Publiez des applications.
4. Configuration de l'accès utilisateur

**Avant de commencer**

Avant de créer la collection, vous devez effectuer les étapes suivantes :

* [S'inscrire](https://azure.microsoft.com/services/remoteapp/) à Azure RemoteApp.
* Collecter des informations sur les utilisateurs auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs.
* Cette procédure part du principe que vous allez utiliser les images de modèle fournies dans le cadre de votre abonnement ou que vous avez déjà téléchargé l'image de modèle à utiliser. Si vous devez télécharger une autre image de modèle, rendez-vous sur la page Images de modèle. Il vous suffit de cliquer sur **télécharger une image de modèle** et de suivre les étapes de l'Assistant.
* Vous souhaitez utiliser l’image d’Office 365 ProPlus ? Pour plus d’informations, cliquez [ici](remoteapp-officesubscription.md).
* Vous voulez fournir des applications personnalisées ou des programmes métier ? Créez une [image](remoteapp-imageoptions.md) et utilisez-la dans votre collection cloud.
* Déterminez si vous devez vous connecter à un réseau virtuel. Si vous choisissez de vous connecter à un réseau virtuel, vérifiez qu’il respecte les [instructions de dimensionnement](remoteapp-vnetsizing.md) et qu’il [peut se connecter à RemoteApp](remoteapp-vnet.md). Pour plus d'informations, passez en revue l'article sur la [planification d'un réseau virtuel](remoteapp-planvnet.md).
* Si vous utilisez un réseau virtuel, déterminez si vous souhaitez le joindre à votre domaine Active Directory local.

## Étape 1 : créer une collection cloud avec ou sans réseau virtuel
Pour **créer une collection uniquement dans le cloud**, procédez comme suit :

1. Accédez à la page RemoteApp du portail de gestion Azure.
2. Cliquez sur **Nouveau > Création rapide**.
3. Entrez le nom de votre collection et sélectionnez votre région.
4. Choisissez le plan que vous souhaitez utiliser (de base ou standard).
5. Choisissez le modèle à utiliser pour cette collection.
   
    **Conseil :** votre abonnement à RemoteApp s'accompagne d'[images de modèle](remoteapp-images.md) qui contiennent des programmes Office 365 ou Office 2013 (à des fins d'évaluation), certains étant publiés (tels que Word) et d'autres étant prêts pour la publication. Vous pouvez également créer une [image](remoteapp-imageoptions.md) et l'utiliser dans votre collection cloud.
6. Cliquez sur **Créer une collection RemoteApp**.
   
    **Important :** la configuration de votre collection peut prendre jusqu'à 30 minutes.

Après avoir créé votre collection Azure RemoteApp, double-cliquez sur son nom. La page **Démarrage rapide** s’affiche alors et vous permet de terminer la configuration de la collection.

Pour créer une **collection dans le cloud et dans un réseau virtuel**, procédez comme suit :

1. Accédez à la page RemoteApp du portail de gestion.
2. Cliquez sur **Nouveau** > **Créer avec un réseau virtuel**.
3. Entrez un nom pour votre collection.
4. Choisissez le plan que vous souhaitez utiliser (de base ou standard).
5. Choisissez le réseau virtuel que vous avez déjà créé. Vous ne savez pas comment faire ? Pour l’instant, les étapes se trouvent dans la rubrique [hybride](remoteapp-create-hybrid-deployment.md).
6. Décidez si vous souhaitez joindre votre collection à votre domaine. Dans l’affirmative, vous devez utiliser AD Connect pour intégrer Azure AD et votre environnement Active Directory. La procédure à suivre est traitée à l’**étape 2**.
7. Cliquez sur **Créer une collection RemoteApp**.

## Étape 2 : configuration de la synchronisation d'annuaires Active Directory (facultatif)
Si vous souhaitez utiliser Active Directory, Azure RemoteApp requiert une synchronisation d’annuaires entre Azure Active Directory et votre Active Directory local afin de synchroniser les utilisateurs, contacts et mots de passe dans votre locataire Azure Active Directory. Consultez [Configuration d'Active Directory pour Azure RemoteApp](remoteapp-ad.md) pour obtenir des informations sur la planification. Vous pouvez également accéder directement à [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) pour obtenir des informations.

## Étape 3: publier des applications
Une application Azure RemoteApp correspond à l’application ou au programme que vous fournissez à vos utilisateurs. Elle se trouve dans l'image de modèle que vous avez téléchargée pour la collection. Quand un utilisateur accède à une application, celle-ci semble s'exécuter dans son environnement local. En réalité, elle s'exécute sur une machine virtuelle dans Azure.

Avant que vos utilisateurs puissent accéder à des applications, vous devez les publier. Le fait de publier des applications permet à vos utilisateurs d'y accéder via le client Bureau à distance.

Vous pouvez publier plusieurs applications dans votre collection RemoteApp. Dans la page de publication, cliquez sur **Publier** pour ajouter un programme. Vous pouvez publier une application à partir du menu **Démarrer** de l’image de modèle ou en indiquant le chemin dans l’image de modèle de l’application. Si vous choisissez d'ajouter une application à partir du menu **Démarrer**, sélectionnez l'application à publier. Si vous choisissez la deuxième option, indiquez un nom pour l'application ainsi que le chemin d'accès à son répertoire d'installation dans l'image de modèle.

## Étape 4 : configuration de l'accès utilisateur
Maintenant que vous avez créé votre collection, vous devez ajouter les utilisateurs qui seront autorisés à utiliser vos ressources distantes. Si vous utilisez Active Directory, les utilisateurs auxquels vous accordez l’accès doivent exister dans le locataire Active Directory associé à l’abonnement utilisé pour créer cette collection.

1. Sur la page Démarrage rapide, cliquez sur **Configurer l'accès utilisateur**.
2. Entrez le compte professionnel (à partir d'Active Directory) ou le compte Microsoft auquel vous souhaitez accorder l'accès.
   
   **Remarques :**
   
   assurez-vous d'utiliser le format « utilisateur@domaine.com ».
   
   Si vous utilisez Office 365 ProPlus dans votre collection, vous devez utiliser les identités Active Directory de vos utilisateurs. Cela permet de valider la licence.
3. Une fois les utilisateurs validés, cliquez sur **Enregistrer**.

## Étapes suivantes
Félicitations ! Vous avez créé et déployé correctement votre collection cloud Azure RemoteApp. L'étape suivante consistera à faire en sorte que vos utilisateurs téléchargent et installent le client Bureau à distance. L’URL du client est disponible dans la page Démarrage rapide de RemoteApp. Les utilisateurs peuvent à présent se connecter au client et accéder aux applications que vous avez publiées.

### Vos commentaires nous aideront à mieux vous servir
Saviez-vous qu’en plus de noter cet article et de rédiger des commentaires ci-dessous, vous pouviez modifier l’article lui-même ? Il manque des informations ? Des informations sont erronées ? Certains passages ne sont pas clairs ? Faites défiler l’écran vers le haut et cliquez sur **Modifier sur GitHub** pour apporter des modifications. Nous les passerons ensuite en revue, et une fois que nous les aurons confirmées, vos modifications et les améliorations seront visibles ici.

<!---HONumber=AcomDC_0817_2016-->