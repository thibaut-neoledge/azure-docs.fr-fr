1.  Connectez-vous au [portail de gestion Azure][1], cliquez sur
    **Mobile Services**, puis sur l'application.
    
    ![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2.  Cliquez sur l'onglet **API**, puis sur **Create a custom API**.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)
    
    La boîte de dialogue **Create a new custom API** s'affiche.

3.  Tapez *completeall* dans **API name**, puis cliquez sur le bouton de
    vérification.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)
    
    L'API est créée.
    
    > [WACOM.NOTE] Les autorisations par défaut sont définies, ce qui
    > signifie que tous les utilisateurs de l'application peuvent
    > appeler l'API personnalisée. Toutefois, la clé de l'application
    > n'étant pas distribuée ou stockée de façon sécurisée, elle ne peut
    > pas être considérée comme des informations d'identification
    > sécurisées. De ce fait, vous devez restreindre l'accès aux
    > utilisateurs authentifiés pour les opérations qui modifient les
    > données ou affectent le service mobile.

4.  Cliquez sur la nouvelle entrée **completeall** dans la table API.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5.  Cliquez sur l'onglet **Script**, remplacez le code existant par le
    code suivant, puis cliquez sur **Enregistrer** :
    
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
    
    Ce code utilise l'[objet mssql][2] pour accéder à la table
    **todoitem** directement pour définir l'indicateur complété pour
    tous les éléments. La fonction **exports.post** étant utilisée, les
    clients envoient une requête POST pour effectuer l'opération. Le
    nombre de lignes modifiées est renvoyé au client sous la forme d'une
    valeur entière.

> [WACOM.NOTE] Les objets [request][3] et [response][4] fournis aux fonctions API personnalisées sont
> implémentés par la [bibliothèque Express.js][5]. Pour plus d'informations, consultez la page [API
> personnalisée][6].

Ensuite, vous allez modifier l'application de démarrage rapide pour
ajouter un bouton et du code qui appelle de manière asynchrone la
nouvelle API personnalisée.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: https://manage.windowsazure.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554218.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/dn303373.aspx
[5]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
