<properties
	pageTitle="Autorisation côté service des utilisateurs dans Mobile Services avec un backend JavaScript | Centre de développement pour appareils mobiles"
	description="Découvrez comment autoriser des utilisateurs sur le backend JavaScript Azure Mobile Services."
	services="mobile-services"
	documentationCenter=""
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="javascript"
	ms.date="05/20/2015"
	ms.author="krisragh"/>

# Autorisation côté service des utilisateurs de Mobile Services

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

Cette rubrique montre comment utiliser des scripts côté serveur pour autoriser des utilisateurs. Dans ce didacticiel, vous inscrivez les scripts dans Azure Mobile Services, filtrez des requêtes en fonction de l’ID utilisateur et permettez aux utilisateurs d’accéder uniquement à leurs propres données. Le filtrage des résultats de la requête d'un utilisateur par l'ID d'utilisateur est la forme d’autorisation la plus basique. Selon votre scénario spécifique, vous pouvez également créer des tables ou rôles utilisateurs pour effectuer le suivi d’informations d'autorisation utilisateur plus détaillées, telles que les points de terminaison auxquels un utilisateur donné peut accéder.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel [Ajout de l'authentification à une application Mobile Services existante]. Veuillez suivre [Ajout de l'authentification à une application Mobile Services existante] tout d'abord.

## <a name="register-scripts"></a>Inscription des scripts

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur votre service mobile. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

2. Cliquez sur **Script**, sélectionnez l’opération **Insérer**, remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

	Ce script ajoute l’ID utilisateur de l’utilisateur authentifié à l’élément avant l’insertion.

    >[AZURE.NOTE]Assurez-vous que le [schéma dynamique](https://msdn.microsoft.com/library/azure/jj193175.aspx) est activé. Dans le cas contraire, la colonne *userId* n'est pas ajoutée automatiquement. Ce paramètre est activé par défaut pour un nouveau service mobile.

3. De la même façon, remplacez l'opération **Read** existante par la fonction suivante : Ce script filtre les objets TodoItem renvoyés, de façon à ce qu’un utilisateur reçoive uniquement les éléments qu’il a insérés.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

## <a name="test-app"></a>Test de l'application

1. Remarquez que quand vous exécutez votre application côté client, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table _TodoItem_, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne user ID et comportent maintenant des valeurs null. Vérifiez que les nouveaux éléments ajoutés ont une valeur userId associée dans la table _TodoItem_.

2. Si vous disposez de comptes de connexion supplémentaires, assurez-vous que les utilisateurs peuvent uniquement afficher leurs propres données en fermant, puis supprimant l’application, avant de l’exécuter de nouveau. Une fois que la boîte de dialogue des informations d’identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous la session précédente ne s’affichent pas.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Ajout de l'authentification à une application Mobile Services existante]: /develop/mobile/tutorials/get-started-with-users-ios

[portail de gestion Azure]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->