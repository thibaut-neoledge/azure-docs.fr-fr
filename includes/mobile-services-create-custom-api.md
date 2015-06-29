

1. Connectez-vous au portail de gestion Azure, cliquez sur **Mobile Services**, puis sélectionnez le service mobile.

2. Cliquez sur l'onglet **API**, puis sur **Créer**. La boîte de dialogue **Create a new custom API** s’affiche.

3. Tapez _completeall_ dans **Nom API**, puis cliquez sur le bouton de vérification pour créer l'API.

	> [AZURE.NOTE]Avec les autorisations par défaut, toute personne avec la clé d’application peut appeler l’API personnalisée. Toutefois, la clé d’application n’est pas considérée comme une information d’identification sécurisée, car elle peut ne pas être distribuée ou stockée de manière sécurisée. Restreignez l’accès aux utilisateurs authentifiés pour renforcer la sécurité.

4. Cliquez sur **completeall** dans la table API.

5. Cliquez sur l'onglet **Script**, remplacez le code existant par le code suivant, puis cliquez sur **Enregistrer**. Ce code utilise l'[objet mssql] pour accéder à la table **todoitem** directement pour définir l'indicateur `complete` pour tous les éléments. La fonction **exports.post** étant utilisée, les clients envoient une requête POST pour effectuer l’opération. Le nombre de lignes modifiées est renvoyé au client sous la forme d’une valeur entière.


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



> [AZURE.NOTE]Les objets <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">request</a> et <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">response</a> fournis aux fonctions API personnalisées sont implémentés par la <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">bibliothèque Express.js</a>. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">API personnalisée</a>.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[objet mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=58_postMigration-->