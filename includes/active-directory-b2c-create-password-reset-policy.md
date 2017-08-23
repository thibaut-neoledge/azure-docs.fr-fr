Pour activer la réinitialisation affinée du mot de passe sur votre application, vous devez créer une stratégie de réinitialisation du mot de passe. Notez que l’option de réinitialisation du mot de passe au niveau du client est spécifiée [ici](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Cette stratégie décrit les expériences des clients lors de la réinitialisation du mot de passe et le contenu des jetons que l’application reçoit en cas d’opération réussie.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Dans la section stratégie des paramètres, sélectionnez **Stratégies de modification de mot de passe** et cliquez sur **+ Ajouter**.

![Sélectionnez des stratégies d’inscription ou de connexion et cliquez sur Ajouter.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Entrez un **Nom** de stratégie servant de référence pour votre application. Par exemple, entrez : `SSPR`.

Cliquez sur **Fournisseurs d’identité** et sélectionnez **Réinitialiser le mot de passe à l’aide de l’adresse e-mail**. Cliquez sur **OK**.

![Sélectionnez la réinitialisation du mot de passe à l’aide de l’adresse e-mail et cliquez sur OK.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Cliquez sur **Revendications de l’application**. Choisissez les revendications à renvoyer à votre application dans les jetons d’authentification après une expérience de réinitialisation du mot de passe réussie. Par exemple, sélectionnez **ID d’objet de l’utilisateur**.

![Sélectionnez des revendications d’application et cliquez sur le bouton OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Cliquez sur **Créer** pour ajouter la stratégie. La stratégie est répertoriée en tant que **B2C_1_SSPR**. Le préfixe **B2C_1_** est ajouté au nom.

Ouvrez la stratégie en sélectionnant **B2C_1_SSPR**. Vérifiez les paramètres spécifiés dans la table, puis cliquez sur **Exécuter maintenant**.

![Sélectionner la stratégie et l’exécuter](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Paramètre      | Valeur  |
| ------------ | ------ |
| **Applications** | Application de Contoso B2C |
| **Sélectionner l’URL de réponse** | `https://localhost:44316/` |

Un nouvel onglet de navigateur s’ouvre et vous pouvez vérifier l’expérience du client consistant à réinitialiser un mot de passe dans votre application.

> [!NOTE]
> La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
>
