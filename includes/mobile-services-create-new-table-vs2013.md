

Avant de pouvoir stocker des données dans le nouveau service mobile, vous devez créer une table de stockage dans le service. Cette procédure utilise Visual Studio 2013 pour créer une table dans le service mobile. Ensuite, vous mettez à jour l’application pour utiliser Mobile Services afin de stocker les éléments dans Azure et non dans la collection locale.


1. Dans l'Explorateur de serveurs, développez **Azure Mobile Services**, cliquez avec le bouton droit sur le service mobile, cliquez sur **Créer une table**, puis tapez `TodoItem` dans **Nom de la table**.

	![créer une table dans VS 2013](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2. Développez **Avancé**, vérifiez que les autorisations par défaut sur la table sont **Anybody with the Application Key**, puis cliquez sur **Créer**.

	La table TodoItem est créée sur le serveur et quiconque possédant la clé de l’application peut accéder aux données de la table et les modifier sans devoir être authentifié au préalable.

	>[AZURE.NOTE]La clé de l’application est distribuée avec l’application. Étant donné que cette clé n’est pas distribuée de façon sécurisée, elle ne peut pas être considérée comme un jeton de sécurité. Pour sécuriser l’accès aux données du service mobile, les utilisateurs doivent s’authentifier dans un premier temps. Pour plus d’informations, consultez la page [Autorisations](http://msdn.microsoft.com/library/windowsazure/jj193161.aspx).
	>
	>Des tables sont créées avec les colonnes __createdAt, __updatedAt et __version. Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet JSON dans la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).




<!--HONumber=54-->