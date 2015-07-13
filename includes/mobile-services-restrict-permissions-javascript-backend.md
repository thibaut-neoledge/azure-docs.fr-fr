
Pour sécuriser vos points de terminaison, vous devez limiter l’accès aux clients authentifiés.

1. Dans le [portail de gestion Azure](https://manage.windowsazure.com/), accédez à votre service mobile, puis cliquez sur **Données** > nom de votre table (** TodoItem **) > **Autorisations**. 

2. Définissez toutes les autorisations d’opération de table sur **Seuls les utilisateurs authentifiés**.

	 Cela permet de s’assurer que toutes les opérations effectuées sur la table nécessitent un utilisateur authentifié, ce qui est requis pour ce didacticiel. Vous pouvez définir différentes autorisations sur chaque opération pour prendre en charge votre scénario spécifique.

<!---HONumber=62-->