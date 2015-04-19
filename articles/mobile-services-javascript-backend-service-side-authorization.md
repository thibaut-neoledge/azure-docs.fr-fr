<properties
	pageTitle="Autorisation côté service des utilisateurs dans Mobile Services avec un serveur principal JavaScript | Centre de développement mobile"
	description="Découvrez comment autoriser des utilisateurs sur le serveur principal JavaScript d'Azure Mobile Services."
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Autorisation côté service des utilisateurs de Mobile Services

> [AZURE.SELECTOR-LIST (Plateforme | Principal)]
- [(Toutes | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Toutes | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

Cette rubrique montre comment utiliser des scripts côté serveur pour autoriser des utilisateurs. Dans ce didacticiel, vous inscrivez les scripts dans Azure Mobile Services, filtrez des requêtes en fonction de l'ID utilisateur et permettez aux utilisateurs d'accéder uniquement à leurs propres données.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel [Ajout de l'authentification à une application Mobile Services existante]. Veuillez suivre [Ajout de l'authentification à une application Mobile Services existante] tout d'abord.

## <a name="register-scripts"></a>Inscription des scripts

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur votre service mobile. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

2. Cliquez sur **Script**, puis sélectionnez l'opération **Insérer**.

   	![][2]

3. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**. Ce script ajoute l'ID utilisateur de l'utilisateur authentifié à l'élément avant l'insertion.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] [Le schéma dynamique doit être activé](https://msdn.microsoft.com/library/azure/jj193175.aspx) pour que cela fonctionne. Ce paramètre est activé par défaut pour les nouveaux services mobiles.

5. De la même façon, remplacez l'opération **Read** existante par la fonction suivante : Ce script filtre les objets TodoItem renvoyés, de façon à ce qu'un utilisateur reçoive uniquement les éléments qu'il a insérés.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>Test de l'application

1. Remarquez que lorsque vous exécutez votre application côté client, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table _TodoItem_, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne user ID et comportent maintenant des valeurs null. Vérifiez que les nouveaux éléments ajoutés ont une valeur userId associée dans la table _TodoItem_.

2. Si vous disposez de comptes de connexion supplémentaires, assurez-vous que les utilisateurs peuvent uniquement afficher leurs propres données en fermant, puis supprimant l'application, avant de l'exécuter de nouveau. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous la session précédente ne s'affichent pas.

<!-- Anchors. -->
[Enregistrement de scripts serveur]: #register-scripts
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Notifications Push de Windows & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Tableau de bord Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Ajout de Mobile Services à une application existante]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
[Ajout de l'authentification à une application Mobile Services existante]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
[Ajout de notifications Push à votre application]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios

[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=45--> 
