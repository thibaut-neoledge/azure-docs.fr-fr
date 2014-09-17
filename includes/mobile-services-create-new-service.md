Suivez cette procédure pour créer un service mobile.

1.  Connectez-vous au [portail de gestion][1].

2.  En bas du volet de navigation, cliquez sur **+NEW**.
    
 	![](./media/mobile-services-create-new-service/plus-new.png)

3.  Développez **Compute** et **Mobile Service**, puis cliquez sur **Create**.
    
 	![](./media/mobile-services-create-new-service/mobile-create.png)
    
 	La boîte de dialogue **New Mobile Service** s'affiche.

4.  Sur la page **Create a mobile service**, sélectionnez **Create a free 20 MB SQL Database**, sélectionnez **Node.js**, puis tapez un nom de sous-domaine pour le nouveau service mobile dans la zone de texte **URL** et patientez lors de la vérification du nom. Une fois la vérification du nom terminée, cliquez sur la flèche droite pour aller sur la page suivante.
    
 	![](./media/mobile-services-create-new-service/mobile-create-page1.png)

 La page **Specify database settings** s’affiche.     <div  class="dev-callout"> 

 	<div class="dev-callout">
 	<b>Remarque</b> 

 	<p>Dans ce didacticiel, vous créez une instance et un serveur de base de données SQL. Vous pouvez réutiliser cette nouvelle base de données et la gérer comme une autre instance de base de données SQL. Si vous disposez déjà d’une base de données dans la même région que le nouveau service mobile, vous pouvez alors choisir <strong>Utiliser une base existante</strong> et sélectionner cette base de données. À cause des coûts supplémentaires liés à la bande passante et en raison des latences élevées, il est déconseillé d’utiliser une base de données dans une autre région.</p></div>
	

1.  Dans **Nom**, tapez le nom de la nouvelle base de données, puis le **Nom de connexion**, qui est le nom de connexion de l'administrateur pour le nouveau serveur de base de données SQL, tapez et confirmez le mot de passe, puis cliquez sur le bouton de vérification pour terminer le processus.
    
 	![](./media/mobile-services-create-new-service/mobile-create-page2.png)

 	<div class="dev-callout">
 	<b>Remarque</b>
 	<p>Lorsque le mot de passe entré ne respecte pas les exigences de complexité ou en cas d'incohérence, un avertissement s'affiche.    Il est recommandé de noter le nom de connexion de l'administrateur et le mot de passe spécifié. Ces informations seront nécessaires à l'avenir pour utiliser l'instance ou le serveur de base de données SQL.</p>
 	</div>

Vous avez créé un service mobile pouvant être utilisé par vos applications mobiles.

<!-- URLs. -->



[1]: https://manage.windowsazure.com/