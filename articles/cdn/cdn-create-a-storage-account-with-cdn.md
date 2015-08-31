<properties 
	pageTitle="Utilisation de CDN | Microsoft Azure"
	description="Découvrez comment utiliser le réseau de distribution de contenu (CDN) Azure pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique."
	services="cdn"
	documentationCenter=".net"
	authors="zhangmanling"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="mazha"/>


# Intégration d’un compte de stockage avec CDN

CDN peut être activé pour mettre du contenu en cache depuis le stockage Azure. Il offre aux développeurs une solution globale pour la diffusion de contenu haut débit en mettant en cache les objets blob et le contenu statique des instances de calcul sur des nœuds physiques aux États-Unis, en Europe, en Asie, en Australie et en Amérique du Sud.


## Étape 1 : création d’un compte de stockage

Utilisez la procédure suivante pour créer un compte de stockage pour un abonnement Azure. Un compte de stockage donne accès aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour l’accès à chacun des composants du service de stockage Azure : services BLOB, services de file d’attente et services de Table. Pour plus d’informations sur les services de stockage Azure, consultez la page [Utilisation des services de stockage Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un co-administrateur de l’abonnement associé.

> [AZURE.NOTE]Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API de gestion des services Azure, consultez la rubrique de référence [Création d’un compte de stockage](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail de gestion Azure].
2.  Dans le coin inférieur gauche, sélectionnez **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez **Data Services**, cliquez sur **Stockage**, puis sur **Création rapide**.

    La boîte de dialogue **Create Storage Account** s’affiche.

    ![Création d’un compte de stockage][create-new-storage-account]

4. Dans le champ **URL**, tapez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.

    Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement. Pour adresser une ressource de conteneur dans le service BLOB, vous utilisez un URI au format suivant, où *&lt;StorageAccountLabel&gt;* fait référence à la valeur entrée dans **Entrer une URL** :

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Important :** l’étiquette de l’URL forme le sous-domaine de l’URI du compte de stockage et doit être unique parmi tous les services hébergés dans Azure.

	Cette valeur est également utilisée comme nom pour ce compte de stockage dans le portail ou lors de l’accès à ce compte par programme.

5.  Dans la liste déroulante **Groupe de régions ou d'affinités**, sélectionnez un groupe de régions ou d'affinités pour le compte de stockage. Sélectionnez un groupe d’affinités plutôt qu’une zone pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Microsoft Azure que vous utilisez. Ce choix peut améliorer les performances et n’entraîne aucun frais supplémentaire.

    **Remarque :** pour créer un groupe d’affinités, ouvrez la zone **Paramètres** du portail de gestion, cliquez sur **Groupes d’affinités**, puis sur **Ajouter un groupe d’affinités** ou **Ajouter**. Vous pouvez également créer et gérer des groupes d’affinités par le biais de l’API Gestion des services Microsoft Azure. Pour plus d'informations, consultez [Opérations sur les groupes d'affinités].

6. Dans la liste déroulante **Subscription**, sélectionnez l’abonnement à utiliser avec le compte de stockage.
7.  Cliquez sur **Create Storage Account**. Le processus de création du compte de stockage peut durer quelques minutes.
8.  Pour savoir si le compte de stockage a bien été créé, regardez si le compte est affiché dans les éléments répertoriés pour **Storage** avec l’état **Onligne**.


## Étape 2 : création d’un point de terminaison CDN pour le compte de stockage

Dès que vous activez un accès au CDN pour un compte de stockage ou un service hébergé, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre du CDN. Si vous modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu n’est pas disponible via le CDN avant son actualisation, c’est-à-dire une fois sa durée de vie écoulée.

**Pour créer un point de terminaison CDN pour votre compte de stockage**

1. Dans le [portail de gestion Azure], dans le volet de navigation, cliquez sur **CDN**.

2. Dans le ruban, cliquez sur **New**. Dans la boîte de dialogue **New**, sélectionnez **App Services**, **CDN**, puis **Quick Create**.

3. Dans la liste déroulante **Origin Domain**, sélectionnez le compte de stockage créé lors de l’étape précédente dans la liste des comptes de stockage disponibles.

4. Cliquez sur le bouton **Create** pour créer le point de terminaison.

5. Une fois le point de terminaison créé, il s’affiche dans la liste des points de terminaison pour l’abonnement. L’affichage sous forme de liste montre l’URL à utiliser pour accéder au contenu mis en cache et le domaine d’origine.

	Le domaine d’origine est l’emplacement à partir duquel le CDN met le contenu en cache. Il peut s’agir d’un compte de stockage ou d’un service cloud. Cet exemple utilise un compte de stockage. Le contenu de stockage est mis en cache dans les serveurs Edge en fonction d’un paramètre de contrôle du cache que vous avez spécifié ou de la méthode heuristique par défaut du réseau de mise en cache. Pour plus d’informations, consultez la page [Comment gérer l’expiration du contenu d’objets BLOB](http://msdn.microsoft.com/library/gg680306.aspx).


    > [AZURE.NOTE]La configuration créée pour le point de terminaison ne sera pas disponible immédiatement, car la propagation de l’enregistrement sur le CDN peut prendre jusqu’à 60 minutes. Les utilisateurs qui tentent d’utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d’état 400 (Requête incorrecte) jusqu’à ce que le contenu soit disponible via le CDN.


## Étape 3 : accès au contenu du CDN

Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse d’un objet blob mis en cache est au format suivant :

http://<*EspaceDeNomsCDN*>.vo.msecnd.net/<*monConteneurPublic*>/<*NomObjetBlob*>


## Étape 4 : suppression du contenu du CDN

Si vous ne souhaitez plus mettre en cache un objet dans le réseau de distribution de contenu (CDN) Azure, vous pouvez procéder comme suit :

-   Vous pouvez changer le statut du conteneur de public à privé. Pour plus d’informations, consultez la page [Limiter l’accès aux conteneurs et aux objets BLOB](http://msdn.microsoft.com/library/dd179354.aspx).
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN à l’aide du portail de gestion.
-   Vous pouvez modifier votre service hébergé pour qu’il ne réponde plus aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN y reste jusqu’à ce que sa durée de vie expire. Après cela, le CDN vérifie si le point de terminaison CDN est encore valide et si l’objet est encore accessible de manière anonyme. S’il ne l’est pas, l’objet n’est plus mis en cache.

La capacité à purger immédiatement le contenu n’est actuellement pas prise en charge sur le portail de gestion Azure. Si vous devez supprimer immédiatement et définitivement le contenu, contactez le [support technique Azure](http://azure.microsoft.com/support/options/).

## Ressources supplémentaires

-   [Création d’un groupe d’affinités dans Azure]
-   [Gestion des comptes de stockage pour un abonnement Azure]
-   [Mappage du contenu CDN à un domaine personnalisé]

[Create Storage Account]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure CDN Node Locations]: cdn-pop-locations.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[billing plan]: /pricing/calculator/?scenario=full
[How to Register a Custom Subdomain Name for Accessing Blobs in Azure]: ../storage-custom-domain-name.md
[Création d’un groupe d’affinités dans Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: cdn-overview.md
[Gestion des comptes de stockage pour un abonnement Azure]: https://msdn.microsoft.com/fr-fr/library/azure/hh531793.aspx
[Mappage du contenu CDN à un domaine personnalisé]: cdn-map-content-to-custom-domain.md


[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

 

<!---HONumber=August15_HO8-->