#### Composants requis
- un compte Azure (vous pouvez en [créer un gratuitement)](https://azure.microsoft.com/free) ;
- un compte [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3).

Pour pouvoir utiliser votre compte OneDrive dans une application logique, vous devez autoriser cette dernière à se connecter à votre compte OneDrive. Heureusement, cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure.

Pour autoriser votre application logique à se connecter à votre compte OneDrive, procédez comme suit :

1. Créez une application logique. Dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis saisissez « OneDrive » dans la zone de recherche. Sélectionnez l’un des déclencheurs ou actions : ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Si vous n’avez créé aucune connexion à OneDrive auparavant, vous êtes invité à vous connecter avec vos informations d’identification OneDrive : ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Sélectionnez **Connexion**, puis indiquez votre nom d’utilisateur et votre mot de passe. Sélectionnez **Connexion** : ![](./media/connectors-create-api-onedrive/onedrive-3.png)

	Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte OneDrive.
4. Sélectionnez **Oui** pour autoriser l’application logique à utiliser votre compte OneDrive : ![](./media/connectors-create-api-onedrive/onedrive-4.png)
5. Vous pouvez voir que la connexion a été créée. Maintenant, effectuez les autres étapes dans votre application logique : ![](./media/connectors-create-api-onedrive/onedrive-5.png)

<!---HONumber=AcomDC_0720_2016-->