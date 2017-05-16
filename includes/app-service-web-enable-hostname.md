Revenez à la fenêtre du navigateur qui contient le portail Azure.

#### <a name="add-hostname"></a>Ajouter un nom d’hôte

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajoutez un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>Valider un nom d’hôte

Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement CNAME précédemment (p. ex `www.contoso.com`), puis cliquez sur **Valider**.

Sélectionnez l’option **CNAME (www.example.com ou tout sous-domaine)** sous l’en-tête **Type d’enregistrement du nom d’hôte**.

Cliquez sur **Ajouter un nom d’hôte** pour ajouter le nom DNS à votre application.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Un certain temps peut être nécessaire pour que votre nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.