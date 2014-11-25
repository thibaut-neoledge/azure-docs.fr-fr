1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l'onglet **Data**, puis sur **Create**.

    ![][1]

    La boîte de dialogue **Create new table** s’affiche.

3.  Conservez le paramètre **Anybody with the application key** par défaut pour toutes les autorisations, saisissez *Registrations* dans **Table name**, puis cliquez sur le bouton de vérification.

    ![][2]

  La table **Registrations** est créée. Elle stocke les URI des canaux permettant d’envoyer les notifications Push.

Vous pouvez ensuite modifier votre application pour activer les notifications Push.

<!-- URLs -->

  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-create-new-push-table/mobile-services-selection.png
  [1]: ./media/mobile-services-create-new-push-table/mobile-create-table.png
  [2]: ./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png
