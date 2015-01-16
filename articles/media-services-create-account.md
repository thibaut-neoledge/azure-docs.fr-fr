<properties urlDisplayName="How to create" pageTitle="Création d'un compte Media Services - Azure" metaKeywords="" description="Explique comment créer un compte Media Services dans Azure." metaCanonical="" services="media-services" documentationCenter="" title="How to Create a Media Services Account" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />




#Création d'un compte Media Services

Le portail de gestion Azure permet de créer rapidement un compte Azure Media Services. Vous pouvez utiliser ce dernier pour accéder à Media Services afin de stocker, de chiffrer, d'encoder, de gérer et de diffuser du contenu multimédia dans Azure. Lorsque vous créez un compte Media Services, vous créez également un compte de stockage associé ou en utilisez un existant, situé dans la même zone géographique que le compte Media Services. 

Cette rubrique présente l'utilisation de la méthode Création rapide pour créer un compte Media Services, puis l'associer au compte de stockage. 

##Sommaire

-  [Concepts][]
-  [  Création d'un compte Media Services grâce à la méthode Création rapide][]


<h2><a id="concepts"></a>Concepts</h2>
L'accès à Media Services requiert deux comptes associés :

-   **Un compte Media Services**. Votre compte vous donne accès à un ensemble de services Media Services sur le cloud, disponibles dans Azure. Un compte Media Services ne stocke pas de contenu multimédia à proprement parler. Il stocke des métadonnées relatives au contenu multimédia et aux travaux de traitement multimédia dans votre compte. Lorsque vous créez le compte, vous sélectionnez une région Media Services disponible. Cette dernière est un centre de données qui stocke les enregistrements de métadonnées pour votre compte. 
    > [WACOM.NOTE]
    > Les régions disponibles pour Media Services sont les suivantes : **Europe du Nord**, **Europe de l'Ouest**, **Ouest des États-Unis**, **Est des États-Unis**, **Asie du Sud-Ouest**, **Asie du Sud-Est**, **Ouest du Japon**, **Est du Japon**. Media Services n'utilise pas de groupes d'affinités. 
-   **Un compte de stockage associé**. Il s'agit d'un compte de stockage Azure associé à votre compte Media Services. Il permet de stocker des objets blob pour les fichiers multimédia et doit se trouver dans la même zone géographique que le compte Media Services. Lorsque vous créez un compte Media Services, vous pouvez choisir un compte de stockage existant dans la même région ou en créer un. Si vous supprimez un compte Media Services, les objets blob de votre compte de stockage associé ne seront pas supprimés. 

<h2><a id="quick"></a>Utilisation Création d'un compte Media Services grâce à la méthode Création rapide</h2>

1. Dans le [portail de gestion][], cliquez sur **Nouveau**, **Service de média**, puis sur **Création rapide**.
   
	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. Dans **NOM**, entrez le nom du nouveau compte. Un nom de compte Media Services se compose de chiffres ou de lettres en minuscules, sans espaces. Sa longueur est comprise entre 3 et 24 caractères. 

3. Dans **RÉGION**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements de métadonnées pour votre compte Media Services. Seules les régions Media Services disponibles s'affichent dans la liste déroulante. 

4. Dans **COMPTE DE STOCKAGE**, sélectionnez un compte de stockage pour fournir un stockage d'objets blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région. 

5. Si vous avez créé un compte de stockage, entrez un nom dans **NOUVEAU NOM DU COMPTE DE STOCKAGE**. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.

6. Cliquez sur **Création rapide** en bas du formulaire.

	Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.

	La page **Services de média** s'ouvre et affiche le nouveau compte. Lorsque l'état devient Active, cela signifie que le compte a été correctement créé.

	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	Lorsque vous double-cliquez sur le nom de compte, la page Démarrage rapide s'affiche par défaut. Elle vous permet d'effectuer des tâches de gestion également disponibles sur d'autres pages du portail. Par exemple, vous pouvez télécharger un fichier vidéo depuis cette page ou depuis la page CONTENU.

	De plus, vous pouvez afficher du code utilisant le Kit de développement logiciel (SDK) Azure Media Services pour effectuer les tâches suivantes : télécharger, encoder et publier des vidéos. Vous pouvez cliquer sur l'un des liens présents sous la section ÉCRIRE DU CODE, copier le code et l'utiliser dans votre application. 


<!-- Reusable paths. -->

<!-- Anchors. -->
  [Concepts]: #concepts
  [Avant de commencer]: #begin
  [  Création d'un compte Media Services grâce à la méthode Création rapide]: #quick

<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  
  [Portail de gestion]: http://manage.windowsazure.com/



<!--HONumber=35.1-->
