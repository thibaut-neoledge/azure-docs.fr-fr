
# Utilisation du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure offre aux développeurs une solution globale pour la diffusion de contenu haut débit en mettant en cache les objets blob et le contenu statique des instances de calcul sur des nœuds physiques aux États-Unis, en Europe, en Asie, en Australie et en Amérique du Sud. Pour obtenir la liste actuelle des emplacements de nœuds CDN, consultez la page [Emplacements des nœuds CDN Azure][1].

Cette procédure comprend les étapes suivantes :

* [Étape 1 : Création d'un compte de stockage](#Step1)
* [Étape 2 : Création d'un point de terminaison CDN pour le compte de stockage](#Step2)
* [Étape 3 : Accès au contenu du CDN](#Step3)
* [Étape 4 : Suppression du contenu du CDN](#Step4)

Les avantages de l'utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

* de meilleures performances et une expérience utilisateur plus agréable pour les utilisateurs finaux qui se trouvent éloignés d'une source de contenu et qui utilisent des applications ayant recours à de nombreux << trajets Internet >> pour charger le contenu ;
* une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début d'un événement comme le lancement d'un produit.

Les clients du CDN existants peuvent désormais utiliser le CDN Azure dans le [portail de gestion Azure][2]. Le CDN est une fonctionnalité sous forme de module, ajoutée à votre abonnement et qui présente un [profil de facturation](/en-us/pricing/calculator/?scenario=full) distinct.

<a id="Step1"> </a>

<h2>Étape 1 : Création d'un compte de stockage</h2>


Utilisez la procédure suivante pour créer un compte de stockage pour un abonnement Azure. Un compte de stockage donne accès aux services de stockage Windows Azure. Le compte de stockage représente le niveau le plus élevé de l'espace de noms pour l'accès à chacun des composants du service de stockage Azure : services Blob, services de File d'attente et services de Table. Pour plus d'informations sur les services de stockage Azure, consultez la page [Utilisation des services de stockage Azure][3].

Pour créer un compte de stockage, vous devez être l'administrateur de service ou un co-administrateur de l'abonnement associé.

 
<div  class="dev-callout">
<strong>Remarque</strong>
<p>Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API de gestion des services Azure, consultez la rubrique de référence <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/hh264518.aspx">Création d’un compte de stockage</a>.</p>
</div>

 **Création d'un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail de gestion Azure][2]. 2.  Dans le coin inférieur gauche, cliquez sur **New**, puis sur **Storage**.
3.  Cliquez sur **Create**.
    
    La boîte de dialogue **Create Storage Account** s'affiche.
    
    ![Créer un compte de
    stockage](./media/cdn/CDN_CreateNewStorageAcct.png)

4.  Dans le champ **URL**, tapez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.
    
    Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement. Pour adresser une ressource de conteneur dans le service BLOB, vous utilisez un URI au format suivant, où *<LibelléCompteStockage>* fait référence à la valeur entrée dans **Enter a URL** :
    
    http://*<LibelléCompteStockage>*.blob.core.windows.net/*<monconteneur>*
    
    **Important :** l'étiquette de l'URL forme le sous-domaine de l'URI du compte de stockage et doit être unique parmi tous les services hébergés dans Azure.
    
    Cette valeur est également utilisée comme nom pour ce compte de stockage dans le portail ou lors de l'accès à ce compte par programme.

5.  Dans la liste déroulante **Region/Affinity Group**, sélectionnez un emplacement géographique pour le compte de stockage. Vous pouvez également utiliser un groupe d'affinités. Pour plus d'informations sur la création d'un groupe d'affinités, consultez la page [Création d'un groupe d'affinités dans Azure][4].
6.  Dans la liste déroulante **Subscription**, sélectionnez l'abonnement à utiliser avec le compte de stockage.
7.  Cliquez sur **Create Storage Account**. Le processus de création du compte de stockage peut durer quelques minutes.
8.  Pour savoir si le compte de stockage a bien été créé, regardez si le compte est affiché dans les éléments répertoriés pour **Storage** avec l'état **Onligne**.

<a id="Step2"> </a>

<h2>Étape 2 : Création d'un point de terminaison CDN pour le compte de stockage</h2>


Dès que vous activez un accès au CDN pour un compte de stockage ou un service hébergé, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre du CDN. Si vous modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu n'est pas disponible via le CDN avant son actualisation, c'est-à-dire une fois sa durée de vie écoulée.

**Création d'un point de terminaison CDN pour votre compte de stockage**

1.  Dans le [portail de gestion Azure][2], dans le volet de navigation, cliquez sur **CDN**.

2.  Dans le ruban, cliquez sur **New**. Dans la boîte de dialogue **New**, sélectionnez **App Services**, **CDN**, puis **Quick Create**.

3.  Dans la liste déroulante **Origin Domain**, sélectionnez le compte de stockage créé lors de l'étape précédente dans la liste des comptes de stockage disponibles.

4.  Cliquez sur le bouton **Create** pour créer le point de terminaison.

5.  Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour l'abonnement. L'affichage sous forme de liste montre l'URL à utiliser pour accéder au contenu mis en cache et le domaine d'origine.
    
    Le domaine d'origine est l'emplacement à partir duquel le CDN met le contenu en cache. Il peut s'agir d'un compte de stockage ou d'un service cloud. Cet exemple utilise un compte de stockage. Le contenu de stockage est mis en cache dans les serveurs Edge en fonction d'un paramètre de contrôle du cache que vous avez spécifié ou de la méthode heuristique par défaut du réseau de mise en cache. Pour plus d'informations, consultez la page [Comment gérer l'expiration du contenu d'objets BLOB][5].
    
    

**Remarque**
La configuration créée pour le point de terminaison ne sera pas disponible immédiatement, car la propagation de l'enregistrement sur le CDN peut prendre jusqu'à 60 minutes. Les utilisateurs qui tentent d'utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d'état 400 (Requête incorrecte) jusqu'à ce que le contenu soit disponible via le CDN.


    


<a id="Step3"> </a>

<h2>Étape 3 : Accès au contenu du CDN</h2>


Pour accéder au contenu mis en cache sur le CDN, utilisez l'URL CDN fournie dans le portail. L'adresse d'un objet blob mis en cache est au format suivant :

http://<*EspaceDeNomsCDN*>.vo.msecnd.net/<*monConteneurPublic*>/<*NomObjetBlob*>

<a id="Step4"> </a>

<h2>Étape 4 : Suppression du contenu du CDN</h2>


Si vous ne souhaitez plus mettre en cache un objet dans le réseau de distribution de contenu (CDN) Azure, vous pouvez procéder comme suit :

* S'il s'agit d'un objet blob Azure, vous pouvez le supprimer du conteneur public.
* Vous pouvez changer le statut du conteneur de public à privé. Pour plus d'informations, consultez la page [Limiter l'accès aux conteneurs et aux objets BLOB][6].
* Vous pouvez désactiver ou supprimer le point de terminaison CDN à l'aide du portail de gestion.
* Vous pouvez modifier votre service hébergé pour qu'il ne réponde plus aux demandes de l'objet.

Un objet déjà mis en cache dans le CDN y reste jusqu'à ce que sa durée de vie expire. Après cela, le CDN vérifie si le point de terminaison CDN est encore valide et si l'objet est encore accessible de manière anonyme. S'il ne l'est pas, l'objet n'est plus mis en cache.

Aucun outil de << vidage >> explicite n'est actuellement disponible pour le CDN Azure.

## Ressources supplémentaires

* [Création d'un groupe d'affinités dans Azure][4]
* [Gestion des comptes de stockage pour un abonnement Azure][7]
* [À propos de l'API de gestion du service dans Windows Azure][8]
* [Mappage du contenu CDN à un domaine personnalisé][9]



[1]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg680302.aspx
[2]: https://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee924681.aspx
[4]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh531560.aspx
[5]: http://msdn.microsoft.com/fr-fr/library/gg680306.aspx
[6]: http://msdn.microsoft.com/fr-fr/library/dd179354.aspx
[7]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh531567.aspx
[8]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460807.aspx
[9]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg680307.aspx