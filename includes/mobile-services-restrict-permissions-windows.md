
1. Dans l'Explorateur de serveurs de Visual Studio, développez le nœud **Azure**, **Mobile Services** et votre service mobile.

2. Cliquez avec le bouton droit sur la table **TodoItem**, cliquez sur **Modifier les autorisations**, définissez toutes les autorisations sur **Seuls les utilisateurs authentifiés**, puis cliquez sur **Appliquer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table _TodoItem_ requièrent un utilisateur authentifié.

3. Cliquez avec le bouton droit sur le projet de l'application cliente, cliquez sur **Déboguer**, puis sur **Démarrer une nouvelle instance** ; vérifiez qu'une exception non gérée avec le code d'état 401 (Non autorisé) est levée après le démarrage de l'application.

	Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

<!---HONumber=July15_HO4-->