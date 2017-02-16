
1. Sur l'ordinateur local, connectez-vous au [portail de gestion Azure](http://manager.windowsazure.com) (il s’agit de l’ancien portail).
2. En bas du volet de navigation, sélectionnez **+NOUVEAU** > **Services d’application** > **Service BizTalk** > **Création personnalisée**.
3. Saisissez une valeur dans le champ **Nom du service BizTalk** et sélectionnez une **édition**. 
   
    Ce didacticiel utilise **mobile1**. Vous aurez besoin d'un nom unique pour votre nouveau service BizTalk.
4. Une fois le service BizTalk créé, sélectionnez l’onglet **Connexions hybrides**, puis cliquez sur **Ajouter**.
   
    ![Add Hybrid Connection](./media/hybrid-connections-create-new/3.png)
   
    Cette action crée une connexion hybride.
5. Remplissez les champs **Nom** et **Nom d’hôte** de la connexion hybride et définissez le champ **Port** sur `1433`. 
   
    ![Configure Hybrid Connection](./media/hybrid-connections-create-new/4.png)
   
    Le nom d'hôte correspond au nom du serveur local. Cette action configure la connexion hybride pour pouvoir accéder à SQL Server s'exécutant sur le port 1433. Si vous utilisez une instance SQL Server nommée, utilisez plutôt le port statique que vous avez défini précédemment.
6. Une fois la connexion créée, le statut de la nouvelle connexion indique **Installation locale incomplète**.
7. Revenez à votre service mobile, cliquez sur **Configure**, faites défiler la page jusqu’à la zone **Connexions hybrides**, cliquez sur **Ajouter une connexion hybride** et sélectionnez la connexion hybride que vous venez de créer, puis cliquez sur **OK**.
   
    Cela permet à votre service mobile d’utiliser votre connexion hybride.

À présent, vous devez installer le Gestionnaire de connexions hybrides sur l’ordinateur local.



<!--HONumber=Jan17_HO3-->


