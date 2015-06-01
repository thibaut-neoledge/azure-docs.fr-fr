# Utilisation du réseau de distribution de contenu (CDN) Azure

Le réseau CDN offre aux développeurs une
solution de remise de contenu de large bande passante en mettant en cache les objets blob
et le contenu statique des instances de calcul au niveau des nœuds physiques aux États-Unis,
en Europe, en Asie, en Australie et en Amérique du Sud. Pour obtenir la liste actuelle des
emplacements des nœuds CDN, voir [Emplacements des nœuds CDN Azure].

Cette procédure comprend les étapes suivantes :

* [Étape 1 : Création d'un compte de stockage](#Step1)
* [Étape 2 : création d'un point de terminaison CDN pour le compte de stockage](#Step2)
* [Étape 3 : Accès au contenu du CDN](#Step3)
* [Étape 4 : suppression du contenu CDN](#Step4)

Les avantages de l'utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

-   De meilleures performances et une expérience utilisateur enrichie pour les utilisateurs finaux qui se trouvent éloignés d'une source de contenu et qui utilisent des applications ayant recours à de nombreux 'internet trips' pour charger le contenu ;
-   une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début d'un événement comme le lancement d'un produit.

Les clients du CDN existants peuvent désormais utiliser le CDN Azure dans le [portail de gestion Azure]. Le CDN est une fonctionnalité sous forme de module complémentaire, ajoutée à votre abonnement et qui présente un [profil de facturation] distinct.

<a id="Step1"> </a>
<h2>Étape 1 : création d'un compte de stockage</h2>

Utilisez la procédure suivante pour créer un compte de stockage pour un
abonnement Azure. Un compte de stockage donne accès aux 
services de stockage Azure. Il représente le plus haut niveau
de l'espace de noms pour accéder à chacun des composants des services de
stockage Azure : services Blob, services de File d'attente et services de Table. Pour plus
d'informations sur les services de stockage Azure, voir [Utilisation des
services de stockage Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Pour créer un compte de stockage, vous devez être administrateur de
service ou coadministrateur de l'abonnement associé.

> [AZURE.NOTE] Pour des informations sur la réalisation de cette opération à l'aide de
l'API de gestion des services Azure, voir la rubrique de référence [Créer un compte de stockage](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail de gestion Azure].
2.  Dans le coin inférieur gauche, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez **Data Services**, cliquez sur **Stockage**, puis sur **Création rapide**.

    La boîte de dialogue **Créer un compte de stockage** s'affiche.

    ![Créer un compte de stockage][create-new-storage-account]

4. Dans le champ **URL**, tapez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.

    Cette valeur devient le nom d'hôte contenu dans l'URI qui est utilisé
    pour adresser les ressources d'un objet blob, de file d'attente ou de table de l'abonnement. Pour
    adresser une ressource de conteneur dans le service BLOB, vous devez utiliser un
    URI au format suivant, où *&lt;StorageAccountLabel&gt;* désigne
    la valeur que vous avez tapée dans **Entrez une URL** :

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Important :** l'étiquette de l'URL forme le sous-domaine de l'URI
    du compte de stockage et doit être unique parmi tous les services hébergés dans 
    Azure.

	Cette valeur est également utilisée comme nom pour ce compte de stockage dans le portail ou lors de l'accès à ce compte par programme.

5.  Dans la liste déroulante **Groupe de régions ou d'affinités**, sélectionnez un groupe de régions ou d'affinités pour le compte de stockage. Sélectionnez un groupe d'affinités plutôt qu'une zone pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Microsoft Azure que vous utilisez. Ce choix peut améliorer les performances et n'entraîne aucun frais supplémentaire.  

    **Remarque :** pour créer un groupe d'affinités, ouvrez la zone **Paramètres** du portail de gestion, cliquez sur **Groupes d'affinités**, puis sur **Ajouter un groupe d'affinités** ou **Ajouter**. Vous pouvez également créer et gérer des groupes d'affinités par le biais de l'API de gestion des services Azure. Pour plus d'informations, voir [Opérations sur les groupes d'affinités].

6. Dans la liste déroulante **Abonnement**, sélectionnez l'abonnement avec lequel le compte de stockage sera utilisé.
7.  Cliquez sur **Créer un compte de stockage**. Le processus de création du compte de stockage peut durer quelques minutes.
8.  Pour savoir si le compte de stockage a bien été créé, vérifiez que le compte est affiché dans les éléments répertoriés pour **Stockage** avec le statut **En ligne**.

<a id="Step2"> </a>
<h2>Étape 2 : création d'un point de terminaison CDN pour le compte de stockage</h2>

Une fois que vous avez activé l'accès CDN à un compte de stockage ou à un service hébergé, tous les
objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre CDN. Si vous
modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu
n'est pas disponible via le CDN tant que ce dernier n'a pas actualisé son contenu
lorsque la durée de vie du contenu mis en cache arrive à expiration.

**Pour créer un point de terminaison CDN pour votre compte de stockage**

1. Dans le [portail de gestion Azure], dans le volet de navigation, cliquez sur **CDN**.

2. Dans le ruban, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez successivement **App Services**, **CDN** et **Création rapide**.

3. Dans la liste déroulante **Domaine d'origine**, sélectionnez le compte de stockage que vous avez créé dans la section précédente dans la liste des comptes de stockage disponibles. 

4. Cliquez sur le bouton **Créer** pour créer le point de terminaison.

5. Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour l'abonnement. L'affichage sous forme de liste montre l'URL à utiliser pour accéder au contenu mis en cache et le domaine d'origine. 

	Le domaine d'origine est l'emplacement à partir duquel le CDN met en cache
    du contenu. Il peut s'agir d'un compte de stockage ou d'un service cloud. Cet exemple utilise un compte de stockage. Le contenu de stockage est mis en cache dans les serveurs Edge en fonction d'un paramètre de contrôle du cache que vous avez spécifié ou de la méthode heuristique par défaut du réseau de mise en cache. Pour plus d'informations, voir [Gestion de l'expiration du contenu des objets blob](http://msdn.microsoft.com/library/gg680306.aspx). 


    > [AZURE.NOTE] La configuration créée pour le point de terminaison ne sera
    pas immédiatement disponible. Le délai nécessaire pour la propagation de l'inscription
    à travers le réseau CDN peut prendre jusqu'à 60 minutes. Les utilisateurs qui essaient
    d'utiliser le nom de domaine du CDN sont susceptibles de recevoir le code d'état 400
    (Requête incorrecte) tant que le contenu n'est pas disponible via le CDN.

<a id="Step3"> </a>
<h2>Étape 3 : Accès au contenu du CDN</h2> 

Pour accéder au contenu mis en cache sur le CDN, utilisez l'URL CDN fournie dans le portail. L'adresse d'un objet blob mis en cache est au format suivant :

http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>

<a id="Step4"> </a>
<h2>Étape 4 : Suppression du contenu du CDN</h2>

Si vous ne voulez plus mettre en cache un objet dans le réseau de distribution de contenu (CDN)
Azure, vous pouvez procéder de l'une des manières suivantes :

-   S'il s'agit d'un objet blob Azure, vous pouvez le supprimer du
    conteneur public.
-   Vous pouvez changer le statut du conteneur de public à privé. Pour plus d'informations, voir [Limiter l'accès aux conteneurs et aux objets blob](http://msdn.microsoft.com/library/dd179354.aspx).
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN à l'aide du portail de
    gestion.
-   Vous pouvez modifier votre service hébergé pour qu'il ne réponde plus aux requêtes de l'objet
    .

Un objet déjà mis en cache dans le CDN y reste
tant que sa durée de vie n'est pas arrivée à expiration. À l'expiration de la durée de vie de l'objet,
le CDN vérifie si le point de terminaison CDN est toujours
valide et si l'objet est toujours accessible de façon anonyme. S'il ne l'est pas,
l'objet n'est plus mis en cache.

La capacité à purger immédiatement le contenu n'est actuellement pas prise en charge sur le portail de gestion Azure. Si vous devez supprimer immédiatement et définitivement le contenu, contactez le [support technique Azure](http://azure.microsoft.com/support/options/). 

## Ressources supplémentaires

-   [Création d'un groupe d'affinités dans Azure]
-   [Gestion des comptes de stockage pour un abonnement Azure]
-   [À propos de l'API de gestion des services]
-   [Mappage du contenu CDN à un domaine personnalisé]

  [Création d'un compte de stockage]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Emplacements des nœuds CDN Azure]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Portail de gestion Azure]: https://manage.windowsazure.com/
  [profil de facturation]: /pricing/calculator/?scenario=full
  [Inscription d'un nom de sous-domaine personnalisé pour accéder aux objets blob dans Azure]: http://msdn.microsoft.com/library/windowsazure/ee795179.aspx
  [Création d'un groupe d'affinités dans Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Vue d'ensemble du CDN Azure]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Gestion des comptes de stockage pour un abonnement Azure]: http://msdn.microsoft.com/library/windowsazure/hh531567.aspx
  [À propos de l'API de gestion des services]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Mappage du contenu CDN à un domaine personnalisé]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Ancien portail de gestion]: ../../Shared/Media/previous-portal.png

<!--HONumber=49-->