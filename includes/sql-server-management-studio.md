
* Connectez-vous à votre compte Azure en entrant vos informations d’identification.
  
  Cette méthode est plus rapide et plus facile, mais si vous l’utilisez, vous ne pourrez pas afficher la base de données SQL ou les services mobiles Azure dans la fenêtre **Explorateur de serveurs**.
  
  Dans l’**Explorateur de serveurs**, cliquez sur le bouton **Se connecter à Azure**. Vous pouvez également cliquer avec le bouton droit sur le nœud **Azure**, puis cliquer sur **Se connecter à Azure** dans le menu contextuel.
* Installez un certificat de gestion pour permettre l’accès à votre compte.
  
  Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur le nœud **Azure**, puis cliquez sur **Gérer les abonnements** dans le menu contextuel. Dans la boîte de dialogue **Manage Azure Subscriptions**, cliquez sur l’onglet **Certificates**, puis sur **Import**. Suivez la procédure pour télécharger et importer un fichier d’abonnement (portant l’extension *.publishsettings*) pour votre compte Azure.
  
  > [!NOTE]
  > téléchargez le fichier d’abonnement dans un dossier situé hors de vos répertoires de code source (par exemple, dans le dossier Téléchargements), puis supprimez-le une fois l’importation terminée. Si un utilisateur malveillant accède au fichier d’abonnement, il peut modifier, créer et supprimer vos services Azure.
  > 
  > 
  
  Pour plus d’informations, consultez la page [Connexion à Azure à partir de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).

<!---HONumber=AcomDC_0413_2016-->