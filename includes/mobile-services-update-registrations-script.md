1.  Dans le portail de gestion, cliquez sur l’onglet **Data**, puis sur la table **Registrations**.

    ![][]

2.  Dans **Registrations**, cliquez sur l’onglet **Script**, puis sélectionnez **Insert**.

    ![][1]

    La fonction appelée lors d’une insertion dans la table **Registrations** s’affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registrations');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertChannelIfNotFound });
            function insertChannelIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

Le nouveau script de la fonction insert est enregistré. Il stocke les informations d’inscription dans une nouvelle table.

  []: ./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png
  [1]: ./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png
