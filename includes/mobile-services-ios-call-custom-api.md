## <a name="update-app"></a><span class="short-header">Mise à jour de l'application </span>Mise à jour de l'application pour appeler l'API personnalisée

1.  Créez un bouton qui vous permettra d'appeler l'API personnalisée. Faites glisser un **bouton rectangle arrondi** de la **bibliothèque d'objets** située en bas du volet **Utilitaires** et placez-le sous le champ de texte ou en regard de celui-ci. Double-cliquez pour ajouter le texte **« Tous »**.

    Un nouveau bouton **« Tous »** est alors ajouté.

2.  Ouvrez le fichier de code **QSTodoService.m**, recherchez la méthode `refreshDataOnSuccess` et vérifiez qu'il contient le code suivant :

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion
        {          
            // Create a predicate that finds items where complete is false
            NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

            // Query the TodoItem table and update the items property with the results from the service
            [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
            {
                [self logErrorIfNotNil:error];

                items = [results mutableCopy];

                // Let the caller know that we finished
                completion();
            }];                                 
        }

    Les éléments sont filtrés de manière à ce que les éléments terminés ne soient pas renvoyés par la requête.

3.  Il est maintenant temps d'associer cet objet au code source du contrôleur d'affichage. **Maintenez enfoncée la touche Ctrl en cliquant sur** le nouveau bouton **« Tous »** et faites glisser la souris avant la ligne `@end` du fichier **QSTodoListViewController.h**. Associez l'objet à la nouvelle **Action** intitulée `onCompleteAll` dans **QSTodoListViewController**. Xcode insère automatiquement la ligne suivante avant la ligne `@end` :

           - (IBAction)onCompleteAll:(id)sender;

4.  L'objectif de cette méthode `onCompleteAll` est de gérer l'événement Clic pour le nouveau bouton. Elle appelle une nouvelle méthode `completeAll` qui s'ajoute à notre classe personnalisée et celle-ci envoie à son tour une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles. Modifiez **QSTodoListViewController.m** pour ajouter l'implémentation suivante avant la ligne `@end` :

           - (IBAction)onCompleteAll:(id)sender {
            [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
             {
                 if (error)
                 {
                     NSString* errorMessage = @"There was a problem! ";
                     errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Error!"
                                             message:errorMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 } else {
                     NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];                   
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Success!"
                                             message:successMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 }
             }];
           }

5.  Notez que le code ci-dessus se rapporte à la nouvelle méthode `completeAll` qui n'a pas encore été définie dans **QSTodoService**. Modifiez **QSTodoService.h** et ajoutez la ligne suivante avant la ligne `@end` :

        - (void) completeAll:(MSAPIBlock)completion;

6.  Ajoutez l'implémentation correspondante de `completeAll` dans **QSTodoService.m** avant la ligne `@end`. Comme JavaScript, iOS ne prend pas en charge la sérialisation JSON des types arbitraires. Par conséquent, il utilise également une API relativement simple composée de la méthode `invokeAPI` pour appeler les API personnalisées.

        - (void) completeAll:(MSAPIBlock)completion
        {
            [self.client
             invokeAPI:@"completeall"
             body:nil
             HTTPMethod:@"POST"
             parameters:nil
             headers:nil
             completion:completion ];
        }

## <a name="test-app"></a>Test de l'application

1.  Dans Xcode, sélectionnez un émulateur vers lequel effectuer le déploiement (via iPhone ou iPad) et appuyez sur le bouton **Exécuter** (ou **Cmd+R**) pour régénérer le projet et démarrer l'application. Cela permet d'exécuter votre client Windows Azure Mobile Services, généré à partir du Kit de développement logiciel (SDK) pour iOS, qui interroge les éléments de votre service mobile.

2.  Tapez du texte dans le champ de texte, puis cliquez sur le bouton **+**. Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Répétez l'étape précédente jusqu'à ce que vous ayez ajouté plusieurs éléments dans la liste.

4.  Appuyez sur le bouton **Tous**. Un message d'alerte s'affiche pour indiquer le nombre d'éléments marqués comme terminés, puis la requête filtrée est de nouveau exécutée pour supprimer tous les éléments de la liste.

    ![][]

  []: ./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png
