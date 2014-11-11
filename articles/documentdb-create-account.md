<properties title="How to Create a DocumentDB Account" pageTitle="How to create a DocumentDB account | Azure" description="Find out how to create a DocumentDB account and choose account settings in the Azure Preview portal."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Création d'un compte DocumentDB

Pour utiliser DocumentDB, vous devez créer un compte DocumentDB dans une région géographique disponible. Cette rubrique décrit la procédure à suivre pour créer un compte DocumentDB sur le portail Azure en version préliminaire.

## Sommaire

-   [Création d'un compte DocumentDB][Création d'un compte DocumentDB]
-   [Étapes suivantes][Étapes suivantes]

## <span id="HowTo"></span></a> Création d'un compte DocumentDB

1.  Connectez-vous à la [version préliminaire du portail de gestion Azure][version préliminaire du portail de gestion Azure].
2.  Cliquez sur Nouveau -\> Compte DocumentDB.
    
	![][0]

    Vous pouvez également parcourir la galerie Azure, sélectionner la catégorie « Données, stockage, cache + sauvegarde », choisir **DocumentDB**, puis cliquer sur **Créer**.

    ![][1]

    Dans le volet **Nouveau DocumentDB (version préliminaire)**, indiquez la configuration souhaitée pour le compte DocumentDB.

    ![][2]

    Dans Nom, tapez un nom à utiliser dans l'URI du compte DocumentDB. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser le compte DocumentDB. Ce nom ne peut contenir que des minuscules, des chiffres, le caractère « - » et doit compter entre 3 et 50 caractères.

    *Attention : documents.azure.com sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte DocumentDB.*

    L'**option de tarification** est verrouillée, car la version préliminaire de DocumentDB prend en charge une seule option de tarification standard.

    *Pour plus d'informations, consultez les [informations de tarification de DocumentDB][informations de tarification de DocumentDB]*.

    La **configuration facultative** est utilisée pour indiquer la capacité initiale allouée à votre compte DocumentDB. DocumentDB exploite les unités de capacité pour vous permettre de faire évoluer votre compte DocumentDB. Chaque unité de capacité inclut le stockage et le débit réservés à la base de données. Par défaut, 1 unité de capacité est approvisionnée. Vous pouvez régler le nombre d'unités de capacité disponibles en fonction de votre compte DocumentDB à tout moment en utilisant la [version préliminaire du portail de gestion Azure][version préliminaire du portail de gestion Azure].

    *Pour plus d'informations sur la capacité et le débit du compte DocumentDB, consultez la page [Gestion des capacités et performances de DocumentDB][Gestion des capacités et performances de DocumentDB].*

    Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte DocumentDB. Par défaut, un nouveau groupe de ressources est créé. Cependant, vous pouvez choisir de sélectionner un groupe de ressources existant auquel ajouter votre compte DocumentDB.

    *Pour plus d'informations, consultez la page [Utilisation des groupes de ressources pour gérer vos ressources Azure][Utilisation des groupes de ressources pour gérer vos ressources Azure].*

    Dans **Abonnement**, sélectionnez l'abonnement Azure à utiliser avec le compte DocumentDB.

    *Si votre compte n'a qu'un seul abonnement, il sera automatiquement sélectionné.*

    Utilisez **Emplacement** pour indiquer l'emplacement géographique de l'hébergement de votre compte DocumentDB.

3.  Une fois les options du nouveau compte DocumentDB configurées, cliquez sur **Créer**. La création du compte DocumentDB peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez consulter le tableau d'accueil.
    
	![][3]

    Ou à partir du concentrateur de notification.

    ![][4]

    ![][5]

4.  Une fois le compte DocumentDB créé, il est prêt à être utilisé avec les paramètres par défaut.

    *Remarque : la cohérence par défaut du compte DocumentDB sera configurée sur Session. Vous pouvez régler le paramètre de cohérence par défaut en utilisant la [version préliminaire du portail de gestion Azure][version préliminaire du portail de gestion Azure].*
    
	![][6]

5.  Vous pouvez également accéder à vos comptes DocumentDB existants à partir du volet **Parcourir**.
    
	![][7]

## <span id="NextSteps"></span></a>Étapes suivantes

-   Pour en savoir plus sur DocumentDB, reportez-vous à la documentation Azure DocumentDB à l'adresse [azure.com][azure.com]

<!--Image references-->

  [Création d'un compte DocumentDB]: #Howto
  [Étapes suivantes]: #NextSteps
  [version préliminaire du portail de gestion Azure]: https://portal.azure.com/
  [0]: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [informations de tarification de DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409
  [Gestion des capacités et performances de DocumentDB]: ../documentdb-manage/
  [Utilisation des groupes de ressources pour gérer vos ressources Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
