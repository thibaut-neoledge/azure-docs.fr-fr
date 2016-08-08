### Composants requis
- un compte Azure (vous pouvez en [créer un gratuitement)](https://azure.microsoft.com/free) ;
- un [compte de stockage d’objets blob Azure](../articles/storage/storage-create-storage-account.md), avec le nom de ce compte et sa clé d’accès. Ces informations sont répertoriées dans les propriétés du compte de stockage, dans le Portail Azure. Découvrez plus d’informations sur [Azure Storage](../articles/storage/storage-introduction.md).

Avant d’utiliser votre compte de stockage d’objets blob Azure dans une application logique, connectez-vous à votre compte de stockage d’objets blob Azure. Cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure.

Connectez-vous à votre compte de stockage d’objets blob Azure en suivant ces étapes :

1. Créez une application logique. Dans le concepteur d’applications logiques, ajoutez un déclencheur, puis ajoutez une action. Sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis saisissez « blob » dans la zone de recherche. Sélectionnez une des actions :

	![Étape de création de la connexion au stockage d’objets blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)

2. Si vous n’avez précédemment créé de connexions à Azure Storage, vous devez saisir les détails de connexion dans l’invite qui s’affiche :

	![Étape de création de la connexion au stockage d’objets blob Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)

3. Entrez les détails du compte de stockage. Les propriétés marquées d’un astérisque sont obligatoires.

	| Propriété | Détails |
|---|---|
| Nom de connexion * | Entrez un nom pour votre connexion. |
| Nom du compte de stockage Azure * | Entrez le nom du compte de stockage. Le nom du compte de stockage s’affiche dans les propriétés de stockage sur le portail Azure. |
| Clé d’accès au compte de stockage * | Entrez la clé du compte de stockage. Les clés d’accès sont affichés dans les propriétés de stockage sur le portail Azure. |

	Ces informations d’identification servent à autoriser votre application logique à se connecter et à accéder à vos données.

4. Sélectionnez **Créer**.

5. Vous pouvez voir que la connexion a été créée. Maintenant, effectuez les autres étapes dans votre application logique :

	![Étape de création de la connexion au stockage d’objets blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)

<!---HONumber=AcomDC_0727_2016-->