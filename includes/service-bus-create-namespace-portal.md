1. Connectez-vous au Portail Azure Classic.

2. Dans le volet de navigation de gauche du portail, cliquez sur **Service Bus**.

3. Dans le volet inférieur du portail, cliquez sur **Créer**.

    ![Sélectionner Créer][select-create]
   
4. Dans la boîte de dialogue **Add a new namespace**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.

    ![Nom de l’espace de noms][namespace-name]
  
5. Après avoir vérifié que le nom de l’espace de noms est disponible, choisissez le pays ou la région où votre espace de noms sera hébergé.

6. Gardez la valeur par défaut des autres champs de la boîte de dialogue (**Messagerie** et **Niveau Standard**), puis cliquez sur la coche OK. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.
 
    ![Création réussie][created-successfully]

###Obtenir les informations d’identification
1. Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour afficher la liste des espaces de noms disponibles :
 
    ![Sélectionner Service Bus][select-service-bus]
  
2. Sélectionnez l’espace de noms que vous venez de créer dans la liste affichée :
 
    ![Sélectionner l’espace de noms][select-namespace]
 
3. Cliquez sur **Connection Information**.

    ![Informations de connexion][connection-information]
  
4. Dans le volet **Accès aux informations de connexion**, recherchez la chaîne de connexion contenant la clé SAP et le nom de clé.

    ![Accès aux informations de connexion][access-connection-information]
  
5. Notez la clé ou copiez-la dans le Presse-papiers.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->