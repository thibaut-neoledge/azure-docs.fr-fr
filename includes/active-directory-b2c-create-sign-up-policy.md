Pour permettre l’inscription sur votre application, vous devez créer une stratégie d’inscription. Celle-ci décrit les expériences des clients lors de l’inscription, et le contenu des jetons que l’application reçoit en cas de réussite de l’opération.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Cliquez sur **Stratégies d'inscription**.

Cliquez sur **+Ajouter** en haut du volet.

Le **Nom** détermine le nom de la stratégie d'inscription utilisé par votre application. Par exemple, entrez **SiUp**.

Cliquez sur **Fournisseurs d’identité** et sélectionnez **Inscription par e-mail**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.

Cliquez sur **Attributs d’inscription**. Ici, vous choisissez les attributs que vous souhaitez collecter auprès du client au cours de l’inscription. Par exemple, sélectionnez **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience d’inscription réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal**, **Nouvel utilisateur** et **ID d’objet de l’utilisateur**.

Cliquez sur **Create**. La stratégie créée s’affiche sous la forme **B2C_1_SiUp** (le fragment **B2C\_1\_** est automatiquement ajouté) dans le panneau **Stratégies d’inscription**.

Ouvrez la stratégie en cliquant sur **B2C_1_SiUp**.

Sélectionnez **Contoso B2C app** dans le menu déroulant **Applications** et `https://localhost:44321/` dans le menu déroulant **URL de réponse/URI de redirection**.

Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous inscrire à votre application.

> [!NOTE]
> La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
>