Pour activer la modification de profil dans votre application, vous devez créer une stratégie de modification de profil. Cette stratégie décrit les expériences des clients lors de la modification du profil et le contenu des jetons que l'application reçoit en cas d'opération réussie.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Dans la section stratégie des paramètres, sélectionnez **Stratégies de modification de profil** et cliquez sur **+ Ajouter**.

![Sélectionnez les stratégies de modification de profil et cliquez sur le bouton Ajouter.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Entrez un **Nom** de stratégie servant de référence pour votre application. Par exemple, entrez : `SiPe`.

Cliquez sur **Fournisseurs d’identité** et sélectionnez **Connexion du compte local**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.

![Sélectionnez la Connexion du compte local comme fournisseur d’identité et cliquez sur le bouton OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Cliquez sur **Attributs de profil**. Choisissez les attributs que le client peut afficher et modifier sur son profil. Par exemple, vérifiez **Pays/région**, **Nom d’affichage** et **Code postal**. Cliquez sur **OK**.

![Sélectionnez certains attributs et cliquez sur le bouton OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Cliquez sur **Revendications de l’application**. Choisissez les revendications à renvoyer à votre application dans les jetons d’authentification après une expérience de modification de profil réussie. Par exemple, sélectionnez **Nom d’affichage** et **Code postal**.

![Sélectionnez des revendications d’application et cliquez sur le bouton OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Cliquez sur **Créer** pour ajouter la stratégie. La stratégie est répertoriée en tant que **B2C_1_SiPe**. Le préfixe **B2C_1_** est ajouté au nom.

Ouvrez la stratégie en sélectionnant **B2C_1_SiPe**. Vérifiez les paramètres spécifiés dans la table, puis cliquez sur **Exécuter maintenant**.

![Sélectionner la stratégie et l’exécuter](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Paramètre      | Valeur  |
| ------------ | ------ |
| **Applications** | Application de Contoso B2C |
| **Sélectionner l’URL de réponse** | `https://localhost:44316/` |

Un nouvel onglet de navigateur s’ouvre et vous pouvez vérifier l’expérience du client consistant à modifier un profil telle qu’elle a été configurée.

> [!NOTE]
> La création de la stratégie et les mises à jour peuvent prendre jusqu’à une minute.
>