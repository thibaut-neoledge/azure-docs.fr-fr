Ensuite, vous créez un service mobile pour remplacer la liste en mémoire pour le stockage de données. Suivez cette procédure pour créer un service mobile.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  En bas du volet de navigation, cliquez sur **+NEW**.

    ![plus-new][plus-new]

3.  Développez **Calcul** et **Service Mobile**, puis cliquez sur **Créer**.

    ![mobile-create][mobile-create]

    La boîte de dialogue **Nouveau service mobile** s'affiche.

4.  Sur la page **Create a mobile service**, sélectionnez **Create a free 20 MB SQL Database**, puis entrez un nom de sous-domaine pour le nouveau service mobile dans la zone de texte **URL** et patientez lors de la vérification du nom. Une fois la vérification du nom terminée, cliquez sur la flèche droite pour aller sur la page suivante.

    ![mobile-create-page1][mobile-create-page1]

    La page **Spécifier les paramètres de la base de données** s'affiche.

    <div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Dans ce didacticiel, vous cr&eacute;ez une instance et un serveur de base de donn&eacute;es SQL. Vous pouvez r&eacute;utiliser cette nouvelle base de donn&eacute;es et la g&eacute;rer comme une autre instance de base de donn&eacute;es SQL. Si vous disposez d&eacute;j&agrave; d&rsquo;une base de donn&eacute;es dans la m&ecirc;me r&eacute;gion que le nouveau service mobile, vous pouvez alors choisir <strong>Utiliser une base existante</strong> et s&eacute;lectionner cette base de donn&eacute;es. &Agrave; cause des co&ucirc;ts suppl&eacute;mentaires li&eacute;s &agrave; la bande passante et en raison des latences &eacute;lev&eacute;es, il est d&eacute;conseill&eacute; d&rsquo;utiliser une base de donn&eacute;es dans une autre r&eacute;gion.</p></div>

5.  Dans **Nom**, tapez le nom de la nouvelle base de données, puis le **Nom de connexion**, qui est le nom de connexion de l'administrateur pour le nouveau serveur de base de données SQL, tapez et confirmez le mot de passe, puis cliquez sur le bouton de vérification pour terminer le processus.

	![mobile-create-page2][mobile-create-page2]

	<div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Lorsque le mot de passe entr&eacute; ne respecte pas les exigences de complexit&eacute; ou en cas d'incoh&eacute;rence, un avertissement s'affiche. <br/>Il est recommand&eacute; de noter le nom de connexion de l'administrateur et le mot de passe sp&eacute;cifi&eacute;. Ces informations seront n&eacute;cessaires &agrave; l'avenir pour utiliser l'instance ou le serveur de base de donn&eacute;es SQL.</p> 
	</div>

Vous avez créé un service mobile pouvant être utilisé par vos applications mobiles. Ensuite, vous allez ajouter une nouvelle table dans laquelle stocker les données d’application. Cette table sera utilisée par l’application au lieu de la collection en mémoire.

  [portail de gestion Azure]: https://manage.windowsazure.com/
  [plus-new]: ./media/mobile-services-create-new-service-data/plus-new.png
  [mobile-create]: ./media/mobile-services-create-new-service-data/mobile-create.png
  [mobile-create-page1]: ./media/mobile-services-create-new-service-data/mobile-create-page1.png
  [mobile-create-page2]: ./media/mobile-services-create-new-service-data/mobile-create-page2.png
