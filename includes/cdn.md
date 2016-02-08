# Utilisation du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure offre aux développeurs une solution globale pour la diffusion de contenu haut débit en mettant en cache les objets blob et le contenu statique des instances de calcul sur des nœuds physiques aux États-Unis, en Europe, en Asie, en Australie et en Amérique du Sud. Pour obtenir la liste actuelle des emplacements de nœuds CDN, consultez la page [Emplacements des nœuds CDN Azure].

Cette procédure comprend les étapes suivantes :

* [Étape 1 : création d’un compte de stockage](#Step1)
* [Étape 2 : création d’un point de terminaison CDN pour le compte de stockage](#Step2)
* [Étape 3 : accès à votre contenu CDN](#Step3)
* [Étape 4 : suppression de votre contenu CDN](#Step4)

Les avantages de l’utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

-   de meilleures performances et une expérience utilisateur plus agréable pour les utilisateurs finaux qui se trouvent éloignés d’une source de contenu et qui utilisent des applications ayant recours à de nombreux « trajets Internet » pour charger le contenu ;
-   une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début d’un événement comme le lancement d’un produit.

Les clients existants du CDN peuvent désormais utiliser le CDN Azure dans le [portail Azure Classic]. Le CDN est une fonctionnalité sous forme de module, ajoutée à votre abonnement et qui présente un [profil de facturation] distinct.

<a id="Step1"> </a> <h2>Étape 1 : création d’un compte de stockage</h2>

Utilisez la procédure suivante pour créer un compte de stockage pour un abonnement Azure. Un compte de stockage donne accès aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour l’accès à chacun des composants du service de stockage Azure : services BLOB, services de file d’attente et services de Table. Pour plus d’informations sur les services de stockage Azure, consultez la page [Utilisation des services de stockage Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un co-administrateur de l’abonnement associé.

> [AZURE.NOTE] Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API de gestion des services Azure, consultez la rubrique de référence [Création d’un compte de stockage](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail Azure Classic].
2.  Dans le coin inférieur gauche, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez **Data Services**, cliquez sur **Stockage**, puis sur **Création rapide**.

    La boîte de dialogue **Create Storage Account** s’affiche.

    ![Création d’un compte de stockage][create-new-storage-account]

4. Dans le champ **URL**, tapez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.

    Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement. Pour adresser une ressource de conteneur dans le service BLOB, vous utilisez un URI au format suivant, où *&lt;StorageAccountLabel&gt;* fait référence à la valeur entrée dans **Entrer une URL** :

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Important :** l’étiquette de l’URL forme le sous-domaine de l’URI du compte de stockage et doit être unique parmi tous les services hébergés dans Azure.

	Cette valeur est également utilisée comme nom pour ce compte de stockage dans le portail ou lors de l’accès à ce compte par programme.

5.  Dans la liste déroulante **Groupe de régions ou d'affinités**, sélectionnez un groupe de régions ou d'affinités pour le compte de stockage. Sélectionnez un groupe d’affinités plutôt qu’une région pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Microsoft Azure que vous utilisez. Ce choix peut améliorer les performances et n’entraîne aucun frais supplémentaire.

    **Remarque :** pour créer un groupe d’affinités, ouvrez la zone **Paramètres** du portail de gestion, cliquez sur **Groupes d’affinités**, puis sur **Ajouter un groupe d’affinités** ou **Ajouter**. Vous pouvez également créer et gérer des groupes d’affinités par le biais de l’API de gestion des services Microsoft Azure. Pour plus d'informations, consultez [Opérations sur les groupes d'affinités].

6. Dans la liste déroulante **Subscription**, sélectionnez l’abonnement à utiliser avec le compte de stockage.
7.  Cliquez sur **Create Storage Account**. Le processus de création du compte de stockage peut durer quelques minutes.
8.  Pour savoir si le compte de stockage a bien été créé, regardez si le compte est affiché dans les éléments répertoriés pour **Storage** avec l’état **Onligne**.

<a id="Step2"> </a> <h2>Étape 2 : création d’un point de terminaison CDN pour le compte de stockage</h2>

Dès que vous activez un accès au CDN pour un compte de stockage ou un service hébergé, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre du CDN. Si vous modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu n’est pas disponible via le CDN avant son actualisation, c’est-à-dire une fois sa durée de vie écoulée.

**Pour créer un point de terminaison CDN pour votre compte de stockage**

1. Dans le [portail Azure Classic], dans le volet de navigation, cliquez sur **CDN**.

2. Dans le ruban, cliquez sur **New**. Dans la boîte de dialogue **New**, sélectionnez **App Services**, **CDN**, puis **Quick Create**.

3. Dans la liste déroulante **Origin Domain**, sélectionnez le compte de stockage créé lors de l’étape précédente dans la liste des comptes de stockage disponibles.

4. Cliquez sur le bouton **Create** pour créer le point de terminaison.

5. Une fois le point de terminaison créé, il s’affiche dans la liste des points de terminaison pour l’abonnement. L’affichage sous forme de liste montre l’URL à utiliser pour accéder au contenu mis en cache et le domaine d’origine.

	Le domaine d’origine est l’emplacement à partir duquel le CDN met le contenu en cache. Il peut s’agir d’un compte de stockage ou d’un service cloud. Cet exemple utilise un compte de stockage. Le contenu de stockage est mis en cache dans les serveurs Edge en fonction d’un paramètre de contrôle du cache que vous avez spécifié ou de la méthode heuristique par défaut du réseau de mise en cache.


    > [AZURE.NOTE] La configuration créée pour le point de terminaison ne sera pas disponible immédiatement, car la propagation de l’enregistrement sur le CDN peut prendre jusqu’à 60 minutes. Les utilisateurs qui tentent d’utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d’état 400 (Requête incorrecte) jusqu’à ce que le contenu soit disponible via le CDN.

<a id="Step3"> </a> <h2>Étape 3 : accès au contenu du CDN</h2>

Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse d’un objet blob mis en cache est au format suivant :

http://<*EspaceDeNomsCDN*>.vo.msecnd.net/<*monConteneurPublic*>/<*NomObjetBlob*>

<a id="Step4"> </a> <h2>Étape 4 : suppression du contenu du CDN</h2>

Si vous ne souhaitez plus mettre en cache un objet dans le réseau de distribution de contenu (CDN) Azure, vous pouvez procéder comme suit :

-   S’il s’agit d’un objet blob Azure, vous pouvez le supprimer du conteneur public.
-   Vous pouvez changer le statut du conteneur de public à privé. Pour plus d’informations, consultez la page [Limiter l’accès aux conteneurs et aux objets BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs).
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN à l’aide du portail de gestion.
-   Vous pouvez modifier votre service hébergé pour qu’il ne réponde plus aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN y reste jusqu’à ce que sa durée de vie expire. Après cela, le CDN vérifie si le point de terminaison CDN est encore valide et si l’objet est encore accessible de manière anonyme. S’il ne l’est pas, l’objet n’est plus mis en cache.

La capacité à purger immédiatement le contenu n’est actuellement pas prise en charge sur le portail de gestion Azure. Si vous devez supprimer immédiatement et définitivement le contenu, contactez le [support technique Azure](https://azure.microsoft.com/support/options/).

## Ressources supplémentaires

-   [Création d’un groupe d’affinités dans Azure]
-   [Gestion des comptes de stockage pour un abonnement Azure]
-   [À propos de l’API de gestion des services]
-   [Mappage du contenu CDN à un domaine personnalisé]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Emplacements des nœuds CDN Azure]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [portail Azure Classic]: https://manage.windowsazure.com/
  [profil de facturation]: /pricing/calculator/?scenario=full
  [Création d’un groupe d’affinités dans Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [À propos de l’API de gestion des services]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Mappage du contenu CDN à un domaine personnalisé]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png

<!---HONumber=AcomDC_0128_2016-->