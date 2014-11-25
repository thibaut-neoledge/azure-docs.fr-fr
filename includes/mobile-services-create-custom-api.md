1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l’onglet **API**, puis sur **Create a custom API**.

    ![][1]

    La boîte de dialogue **Create a new custom API** s’affiche.

3.  Tapez *completeall* dans **API name**, puis cliquez sur le bouton de vérification.

    ![][2]

    L’API est créée.

    > [WACOM.NOTE] Les autorisations par défaut sont définies, ce qui signifie que tous les utilisateurs de l'application peuvent appeler l'API personnalisée. Toutefois, la clé de l'application n'étant pas distribuée ou stockée de façon sécurisée, elle ne peut pas être considérée comme une information d'identification sécurisée. De ce fait, vous devez restreindre l'accès aux utilisateurs authentifiés pour les opérations qui modifient les données ou affectent le service mobile.

4.  Cliquez sur la nouvelle entrée **completeall** dans la table API.

    ![][3]

5.  Cliquez sur l’onglet **Script**, remplacez le code existant par le code suivant, puis cliquez sur **Enregistrer** :

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

	Ce code utilise l’[objet mssql][objet mssql] pour accéder à la table **todoitem** directement pour définir l’indicateur complété pour tous les éléments. La fonction **exports.post** étant utilisée, les clients envoient une requête POST pour effectuer l’opération. Le nombre de lignes modifiées est renvoyé au client sous la forme d’une valeur entière.

> [WACOM.NOTE]
> Les objets [request][request] et [response][response] fournis aux fonctions API personnalisées sont implémentés par la [bibliothèque Express.js][bibliothèque Express.js]. Pour plus d'informations, consultez la page [API personnalisée][API personnalisée].

Ensuite, vous allez modifier l'application de démarrage rapide pour ajouter un bouton et du code qui appelle de manière asynchrone la nouvelle API personnalisée.



 

  [0]: ./media/mobile-services-create-custom-api/mobile-services-selection.png
  [1]: ./media/mobile-services-create-custom-api/mobile-custom-api-create.png
  [2]: ./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png
  [3]: ./media/mobile-services-create-custom-api/mobile-custom-api-select2.png



  [portail de gestion Azure]: https://manage.windowsazure.com/
  [objet mssql]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554212.aspx
  [request]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554218.aspx
  [response]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn303373.aspx
  [bibliothèque Express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [API personnalisée]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn280974.aspx