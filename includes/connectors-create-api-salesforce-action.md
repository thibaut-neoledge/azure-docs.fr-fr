Maintenant que vous avez ajouté une condition, il est temps de faire quelque chose d’intéressant avec les données générées par le déclencheur. Procédez comme suit pour ajouter l’action **Salesforce : Obtenir un objet**. Cette action récupérera les données chaque fois qu’un nouveau prospect est créé. Vous allez également ajouter une deuxième action qui utilisera les données de l’action Salesforce - Obtenir un objet pour envoyer un e-mail à l’aide du connecteur Office 365.

Pour configurer cette action, vous devez fournir les informations suivantes. Vous remarquerez qu’il est facile d’utiliser des données générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier :

|Propriété Créer un fichier|Description|
|---|---|
|Type d'objet|Il s’agit du type d’objet Salesforce qui nous intéresse. Par exemple Prospect, Client, etc.|
|ID objet|Cela représente un identificateur pour l’objet.|


1. Sélectionnez le lien **Ajouter une action**. Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer. Pour cet exemple, les actions de Salesforce nous intéressent. ![Image d’action Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)
- Saisissez *salesforce* pour rechercher des actions liées à Salesforce.
- Sélectionnez **Salesforce - Obtenir un objet** en tant qu’action à effectuer. **Remarque** : vous serez invité à autoriser votre application logique à accéder à votre compte Salesforce, si vous ne l'avez pas fait précédemment. ![Image d’action Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)
- Le contrôle **Obtenir un objet**.
- Sélectionnez *prospect* comme type d’objet.
- Sélectionnez le contrôle **ID d’objet**.
- Sélectionnez **...** pour développer la liste de jetons qui peuvent être utilisés comme entrée pour les actions. ![Image d’action Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)
- Le contrôle Sélectionner **ID de prospect** s’ouvre. ![Image d’action Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)
- Notez que le jeton d’ID de prospect est présent dans le contrôle de l’ID d’objet, indiquant que l’action Obtenir un objet recherchera un prospect avec un ID égal à l’ID du prospect qui a déclenché cette application logique. ![Image d’action Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)
- Enregistrez votre travail. Et c’est tout, vous avez ajouté l’action Obtenir un objet à votre application logique. Votre contrôle Obtenir un objet doit ressembler à ceci : ![Image d’action Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)

Maintenant que vous avez ajouté une action pour obtenir un prospect, vous voudrez faire quelque chose d’intéressant avec le prospect nouvellement créé. Dans une entreprise, vous souhaiterez envoyer un courrier électronique pour informer une liste de distribution de la création d’un prospect. Nous allons utiliser le connecteur Office 365 pour envoyer un courrier électronique avec des informations pertinentes à partir du nouvel objet prospect dans Salesforce.

1. Sélectionnez **Ajouter une action** puis saisissez *e-mail* dans le contrôle de recherche. Cela filtre les actions pour laisser celles liées aux envois et réceptions de courrier électronique.
- Sélectionnez l’élément **Office 365 Outlook - Envoyer un e-mail** dans la liste. Si vous n’avez pas encore créé une *connexion* à votre compte Office 365, vous êtes invité à saisir vos informations d’identification Office 365 pour la créer maintenant. Lorsque vous avez terminé, le contrôle **Envoyer un e-mail** s’ouvre. ![Image d’action Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)
- Saisissez l’adresse de messagerie à laquelle vous souhaitez envoyer un courrier électronique dans le contrôle **À**.
-  Dans le contrôle **Objet**, saisissez *Nouveau prospect créé*, puis sélectionnez le jeton *Société*. Cela affichera le champ *société* à partir du nouveau prospect créé dans Salesforce.
-  Dans le contrôle **corps**, vous pouvez sélectionner un des jetons du nouvel objet de prospect et vous pouvez également saisir le texte que vous souhaitez afficher dans le corps du message électronique. Voici un exemple : ![Image d’action Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)
- Enregistrez votre flux de travail.

Vous avez terminé. Votre application logique est maintenant terminée.

Vous pouvez maintenant tester votre application logique : dans Salesforce, créez un prospect qui répond à la condition que vous avez créée. Si vous avez suivi cette procédure pas à pas en intégralité, créez simplement un prospect avec une adresse de messagerie qui contient *amazon.com*. Votre application logique doit être déclenchée après quelques secondes, et les résultats devraient ressembler à ceci : ![Image d’action Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)

<!---HONumber=AcomDC_0914_2016-->