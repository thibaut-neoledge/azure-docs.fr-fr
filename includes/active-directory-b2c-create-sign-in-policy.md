Pour activer la connexion à votre application, vous devez créer une stratégie de connexion. Cette stratégie décrit les expériences des clients lors de la connexion et le contenu des jetons que l’application reçoit en cas de connexion réussie.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)] Cliquez sur **Stratégies de connexion**.

Cliquez sur **+Ajouter** dans la partie supérieure du panneau.

Le **Nom** détermine le nom de la stratégie de connexion utilisée par votre application. Par exemple, entrez **SiIn**.

Cliquez sur **Fournisseurs d’identité** et sélectionnez **Connexion du compte local**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.

Cliquez sur **Revendications d’application**. Ici, vous choisissez les revendications à renvoyer à votre application dans les jetons après une expérience de connexion réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal** et **ID d’objet de l’utilisateur**. Cliquez sur **OK**.

Cliquez sur **Create**. Notez que la stratégie créée s’affiche sous la forme **B2C_1_SiIn** (le fragment **B2C\_1\_** est automatiquement ajouté) dans le panneau **Stratégies de connexion**.

Ouvrez la stratégie en cliquant sur **B2C_1_SiIn**.

Sélectionnez **Contoso B2C app** dans le menu déroulant **Applications** et `https://localhost:44321/` dans le menu déroulant **URL de réponse/URI de redirection**.

Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous connecter à votre application.

> [!NOTE]
> La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
>