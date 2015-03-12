

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

	![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2. Cliquez sur l'onglet **API**, puis sur **Créer une API personnalisée**.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)

	Cela affiche la boîte de dialogue **Créer une nouvelle API personnalisée**.

3. Tapez _completeall_ dans le **nom de l'API**, puis cliquez sur le bouton de vérification.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)

	L'API est créée.

	> [AZURE.NOTE] Les autorisations par défaut sont définies, ce qui signifie que tous les utilisateurs de l'application peuvent appeler l'API personnalisée. Toutefois, la clé de l'application n'étant pas distribuée ou stockée de façon sécurisée, elle ne peut pas être considérée comme une information d'identification sécurisée. De ce fait, vous devez restreindre l'accès aux utilisateurs authentifiés pour les opérations qui modifient les données ou affectent le service mobile.

4. Cliquez sur la nouvelle entrée **completeall** dans la table API.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5. Cliquez sur l'onglet **Script**, remplacez le code existant par le code suivant, puis cliquez sur **Enregistrer** :

		exports.post = function(request, response) {
			var mssql = request.service.mssql;
			var sql = "UPDATE todoitem SET complete = 1 " + 
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
			mssql.query(sql, {
				success: function(results) {			
					if(results.length == 1)							
						response.send(200, results[0]);			
				}
			})
		};


	Ce code utilise l'[objet mssql] pour accéder à la table **todoitem** directement pour définir l'indicateur complété pour tous les éléments. La fonction **exports.post** étant utilisée, les clients envoient une requête POST pour effectuer l'opération. Le nombre de lignes modifiées est renvoyé au client sous la forme d'une valeur entière.

> [AZURE.NOTE]
> Les objets <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">request</a> et <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">response</a> fournis aux fonctions API personnalisées sont implémentés par la <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">bibliothèque Express.js</a>. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">API personnalisée</a>. 

Ensuite, vous allez modifier l'application de démarrage rapide pour ajouter un bouton et du code qui appelle de manière asynchrone la nouvelle API personnalisée.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet MSSQL]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

\<!--HONumber=42-->
