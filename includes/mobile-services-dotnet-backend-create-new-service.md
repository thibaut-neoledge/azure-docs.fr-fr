Suivez cette procédure pour créer un service mobile.

1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  En bas du volet de navigation, cliquez sur **+NEW**.

    ![][0]

3.  Développez **Calcul** et **Service Mobile**, puis cliquez sur **Créer**.

    ![][1]

    La boîte de dialogue **Nouveau service mobile** s'affiche.

4.  Sur la page **Créer un service mobile**, sélectionnez **Créer une base de données SQL gratuite de 20 Mo**, sélectionnez **.NET**, puis tapez un nom de sous-domaine pour le nouveau service mobile dans la zone de texte **URL** et patientez lors de la vérification du nom. Une fois la vérification du nom terminée, cliquez sur la flèche droite pour aller sur la page suivante.

    ![][2]

   	La page **Spécifier les paramètres de la base de données** s'affiche.

    <div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Dans ce didacticiel, vous cr&eacute;ez une instance et un serveur de base de donn&eacute;es SQL. Vous pouvez r&eacute;utiliser cette nouvelle base de donn&eacute;es et la g&eacute;rer comme une autre instance de base de donn&eacute;es SQL. Si vous disposez d&eacute;j&agrave; d&rsquo;une base de donn&eacute;es dans la m&ecirc;me r&eacute;gion que le nouveau service mobile, vous pouvez alors choisir <strong>Utiliser une base existante</strong> et s&eacute;lectionner cette base de donn&eacute;es. &Agrave; cause des co&ucirc;ts suppl&eacute;mentaires li&eacute;s &agrave; la bande passante et en raison des latences &eacute;lev&eacute;es, il est d&eacute;conseill&eacute; d&rsquo;utiliser une base de donn&eacute;es dans une autre r&eacute;gion.</p></div>

5.  Dans **Nom**, tapez le nom de la nouvelle base de données, puis le **Nom de connexion**, qui est le nom de connexion de l'administrateur pour le nouveau serveur de base de données SQL, tapez et confirmez le mot de passe, puis cliquez sur le bouton de vérification pour terminer le processus.

	![][3]

	<div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Lorsque le mot de passe entr&eacute; ne respecte pas les exigences de complexit&eacute; ou en cas d'incoh&eacute;rence, un avertissement s'affiche. <br/>Il est recommand&eacute; de noter le nom de connexion de l'administrateur et le mot de passe sp&eacute;cifi&eacute;. Ces informations seront n&eacute;cessaires &agrave; l'avenir pour utiliser l'instance ou le serveur de base de donn&eacute;es SQL.</p> 
	</div>

Vous avez créé un service mobile pouvant être utilisé par vos applications mobiles.



  [portail de gestion]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-dotnet-backend-create-new-service/plus-new.png
  [1]: ./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png
  [2]: ./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png
  [3]: ./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png
