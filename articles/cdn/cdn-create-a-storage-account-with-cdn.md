<properties
	pageTitle="Utilisation de CDN | Microsoft Azure"
	description="Découvrez comment utiliser le réseau de distribution de contenu (CDN) Azure pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# Intégration d’un compte de stockage avec CDN

CDN peut être activé pour mettre du contenu en cache depuis le stockage Azure. Il offre aux développeurs une solution globale pour la diffusion de contenu haut débit en mettant en cache les objets blob et le contenu statique des instances de calcul sur des nœuds physiques aux États-Unis, en Europe, en Asie, en Australie et en Amérique du Sud.


## Étape 1 : création d’un compte de stockage

Utilisez la procédure suivante pour créer un compte de stockage pour un abonnement Azure. Un compte de stockage donne accès aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour l’accès à chacun des composants du service de stockage Azure : services BLOB, services de file d’attente et services de Table. Pour plus d'informations, consultez [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un co-administrateur de l’abonnement associé.

> [AZURE.NOTE] Il existe plusieurs méthodes que vous pouvez utiliser pour créer un compte de stockage, y compris le portail Azure et Powershell. Pour ce didacticiel, nous allons utiliser le portail Azure.

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le coin supérieur gauche, sélectionnez **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez **Données + stockage**, puis cliquez sur **Compte de stockage**. Laissez l'option **Classique** sélectionnée comme modèle de déploiement, puis cliquez sur **Créer**.

    Le panneau **Compte de stockage** s'affiche.

    ![Créer un compte de stockage][create-new-storage-account]

4. Dans le champ **Stockage**, tapez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.

    Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement. Pour adresser une ressource de conteneur dans le service BLOB, vous utilisez un URI au format suivant, où *&lt;StorageAccountLabel&gt;* fait référence à la valeur entrée dans **Entrer une URL** :

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Important :** l’étiquette de l’URL forme le sous-domaine de l’URI du compte de stockage et doit être unique parmi tous les services hébergés dans Azure.

	Cette valeur est également utilisée comme nom pour ce compte de stockage dans le portail ou lors de l’accès à ce compte par programme.

5.  Sélectionnez un **niveau de tarification** ou utilisez la valeur par défaut. Pour plus d'informations sur les niveaux de tarification, consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

6.  Sélectionnez ou créez un **groupe de ressources**. Pour plus d'informations sur les groupes de ressources, consultez [Vue d'ensemble d'Azure Resource Manager](resource-group-overview.md#resource-groups).

7. Sélectionnez l'**abonnement** à utiliser avec le compte de stockage.

8.  Cliquez sur **Create**. Le processus de création du compte de stockage peut durer quelques minutes.

9.  Pour savoir si le compte de stockage a bien été créé, regardez si le compte est affiché dans les éléments répertoriés pour **Storage** avec l’état **Onligne**.


## Étape 2 : Créer un profil CDN

Un profil CDN est une collection de points de terminaison CDN. Chaque profil contient un ou plusieurs points de terminaison CDN. Vous pouvez utiliser plusieurs profils pour organiser vos points de terminaison CDN par domaine Internet, application web ou d'autres critères.

> [AZURE.TIP] Si vous disposez déjà d'un profil CDN que vous souhaitez utiliser pour ce didacticiel, passez à l'[Étape 3](#step-3-create-a-new-cdn-endpoint).

**Pour créer un profil CDN**

1. Dans le [portail de gestion Azure](https://portal.azure.com), cliquez sur **Nouveau** dans l'angle supérieur gauche. Dans le panneau **Nouveau**, sélectionnez **Médias + CDN**, puis **CDN**.

    Le panneau du nouveau profil CDN s'affiche.

    ![Nouveau profil CDN][new-cdn-profile]

2. Entrez un nom pour votre profil CDN.

3. Sélectionnez un **niveau tarifaire** ou utilisez la valeur par défaut.

4. Sélectionnez ou créez un **groupe de ressources**. Il n'est pas nécessaire que ce soit le même groupe de ressources que celui de votre compte de stockage.

5. Sélectionnez l'**abonnement** pour ce profil CDN. Il doit s'agir du même abonnement que celui du compte de stockage pour les besoins de ce didacticiel.

6. Sélectionnez un **emplacement**. Il s'agit de l'emplacement Azure où seront stockées les informations de votre profil CDN. Cela n'a aucun impact sur les emplacements de point de terminaison CDN. Il n'est pas nécessaire que ce soit le même emplacement que celui du compte de stockage.

7. Cliquez sur le bouton **Créer** pour créer le profil.

## Étape 3 : Créer un point de terminaison CDN

**Pour créer un point de terminaison CDN pour votre compte de stockage**

1. Dans le [portail de gestion Azure](https://portal.azure.com), accédez à votre profil CDN. Vous l'avez peut-être épinglé au tableau de bord à l'étape précédente. Dans le cas contraire, vous le trouverez en cliquant sur **Parcourir**, puis **Profils CDN** et en cliquant sur le profil auquel vous voulez ajouter le point de terminaison.

    Le panneau du profil CDN s'affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison**.

    ![Bouton Ajouter un point de terminaison][cdn-new-endpoint-button]

    Le panneau **Ajouter un point de terminaison** s’affiche.

    ![Panneau Ajouter un point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN. Ce nom servira à accéder à vos ressources en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Dans la liste déroulante **Type d'origine**, sélectionnez *Stockage*.

5. Dans la liste déroulante **Nom d'hôte d'origine**, sélectionnez votre compte de stockage.

6. Conservez les valeurs par défaut pour le **chemin d'accès d'origine**, l'**en-tête de l'hôte d'origine** et le **port de protocole/origine**. Vous devez spécifier au moins un protocole (HTTP ou HTTPS).

    > [AZURE.NOTE] Cette configuration active tous vos conteneurs visibles publiquement dans votre compte de stockage pour la mise en cache dans le CDN. Si vous souhaitez limiter l'étendue à un seul conteneur, utilisez le **Chemin d'accès d'origine**. Notez que la visibilité du conteneur doit être définie sur « public ».

7. Cliquez sur le bouton **Ajouter** pour créer le point de terminaison.

8. Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour le profil. L’affichage sous forme de liste montre l’URL à utiliser pour accéder au contenu mis en cache et le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.NOTE] Le point de terminaison ne sera pas immédiatement disponible pour utilisation. La propagation de l'inscription à travers le réseau CDN peut prendre jusqu'à 90 minutes. Les utilisateurs qui tentent d'utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d'état 404 jusqu'à ce que le contenu soit disponible via le CDN.


## Étape 4 : accès au contenu du CDN

Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse d’un objet blob mis en cache est au format suivant :

http://<*EndpointName*>.azureedge.net/<*myPublicContainer*>/<*BlobName*>

> [AZURE.NOTE] Dès que vous activez un accès au CDN pour un compte de stockage ou un service hébergé, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre du CDN. Si vous modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu n’est pas disponible via le CDN avant son actualisation, c’est-à-dire une fois sa durée de vie écoulée.

## Étape 5 : Suppression de contenu du CDN

Si vous ne souhaitez plus mettre en cache un objet dans le réseau de distribution de contenu (CDN) Azure, vous pouvez procéder comme suit :

-   Vous pouvez changer le statut du conteneur de public à privé. Pour plus d'informations, consultez [Gestion de l'accès en lecture anonyme aux conteneurs et aux objets blob](../storage/storage-manage-access-to-resources.md).
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN à l’aide du portail de gestion.
-   Vous pouvez modifier votre service hébergé pour qu’il ne réponde plus aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN y reste jusqu'à ce que sa durée de vie expire ou que le point de terminaison soit purgé. Après cela, le CDN vérifie si le point de terminaison CDN est encore valide et si l’objet est encore accessible de manière anonyme. S’il ne l’est pas, l’objet n’est plus mis en cache.


## Ressources supplémentaires

-   [Mappage du contenu CDN à un domaine personnalisé](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0504_2016-->