<properties linkid="manage-services-mediaservices-create-a-media-services-account" urlDisplayName="How to create" pageTitle="Create a Media Services Account - Azure" metaKeywords="" description="Describes how to create a new Media Services account in Azure." metaCanonical="" services="media-services" documentationCenter="" title="How to Create a Media Services Account" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree" />

# Création d'un compte Media Services

Le portail de gestion Azure permet de créer rapidement un compte Azure Media Services. Vous pouvez utiliser ce dernier pour accéder à Media Services afin de stocker, de chiffrer, d'encoder, de gérer et de diffuser du contenu multimédia dans Azure. Lorsque vous créez un compte Media Services, vous créez également un compte de stockage associé ou en utilisez un existant, situé dans la même zone géographique que le compte Media Services.

Cette rubrique présente l'utilisation de la méthode Création rapide pour créer un compte Media Services, puis l'associer au compte de stockage.

## Sommaire

-   [Concepts][Concepts]
-   [Création d'un compte Media Services grâce à la méthode Création rapide][Création d'un compte Media Services grâce à la méthode Création rapide]

## <span id="concepts"></span></a>Concepts

L'accès à Media Services requiert deux comptes associés :

-   **Un compte Media Services**. Votre compte vous donne accès à un ensemble de services Media Services sur le cloud, disponibles dans Azure. Un compte Media Services ne stocke pas de contenu multimédia à proprement parler. Il stocke des métadonnées relatives au contenu multimédia et aux travaux de traitement multimédia dans votre compte. Lorsque vous créez le compte, vous sélectionnez une région Media Services disponible. Cette dernière est un centre de données qui stocke les enregistrements de métadonnées pour votre compte.

    -   **Remarque** Les régions disponibles pour Media Services comprennent les suivantes : **Europe de l'Ouest**, **Asie du Sud-Est**, **Asie de l'Est**, **Europe du Nord**, **Ouest des États-Unis**, **Est des États-Unis**. Media Services n'utilise pas de groupes d'affinités.
-   **Un compte de stockage associé**. Il s'agit d'un compte de stockage Azure associé à votre compte Media Services. Il permet de stocker des objets blob pour les fichiers multimédia et doit se trouver dans la même zone géographique que le compte Media Services. Lorsque vous créez un compte Media Services, vous pouvez choisir un compte de stockage existant dans la même région ou en créer un. Si vous supprimez un compte Media Services, les objets blob de votre compte de stockage associé ne seront pas supprimés.

## <span id="quick"></span></a> Création d'un compte Media Services grâce à la méthode Création rapide

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **New**, sur **Service de média**, puis sur **Création rapide**.

    ![Media Services - Création rapide][Media Services - Création rapide]

2.  Dans **NAME**, entrez le nom du nouveau compte. Un nom de compte Media Services se compose de chiffres ou de lettres en minuscules, sans espaces. Sa longueur est comprise entre 3 et 24 caractères.

3.  Dans **RÉGION**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements de métadonnées pour votre compte Media Services. Seules les régions Media Services disponibles s'affichent dans la liste déroulante.

4.  Dans **COMPTE DE STOCKAGE**, sélectionnez un compte de stockage pour fournir un stockage d'objets blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région.

5.  Si vous avez créé un compte de stockage, entrez un nom dans **NOUVEAU NOM DU COMPTE DE STOCKAGE**. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.

6.  Cliquez sur **Création rapide** en bas du formulaire.

    Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.

    La page **Services de média** s'ouvre et affiche le nouveau compte. Lorsque l'état devient Active, cela signifie que le compte a été correctement créé.

    ![Page Services de média][Page Services de média]

    Lorsque vous double-cliquez sur le nom de compte, la page Démarrage rapide s'affiche par défaut. Elle vous permet d'effectuer des tâches de gestion également disponibles sur d'autres pages du portail. Par exemple, vous pouvez télécharger un fichier vidéo depuis cette page ou depuis la page CONTENU.

    De plus, vous pouvez afficher du code utilisant le Kit de développement logiciel (SDK) Azure Media Services pour effectuer les tâches suivantes : télécharger, encoder et publier des vidéos. Vous pouvez cliquer sur l'un des liens présents sous la section ÉCRIRE DU CODE, copier le code et l'utiliser dans votre application.

<!-- Reusable paths. -->





  [Concepts]: #concepts
  [Création d'un compte Media Services grâce à la méthode Création rapide]: #quick
  [portail de gestion]: http://manage.windowsazure.com/
  [Media Services - Création rapide]: ./media/media-services-create-account/wams-QuickCreate.png
  [Page Services de média]: ./media/media-services-create-account/wams-mediaservices-page.png
