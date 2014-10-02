Lors de la mise en service d’une base de données MongoLab, MongoLab transmet un URI de connexion à Azure en utilisant le format de la chaîne de connexion standard de MongoDB. Cette valeur est utilisée pour initier une connexion MongoDB via le pilote MongoDB de votre choix. Pour plus d’informations sur les chaînes de connexion, consultez la page [Connexions][] sur mongodb.org.

**Cet URI contient votre nom d’utilisateur et votre mot de passe pour la base de données. Considérez ces informations comme étant sensibles, ne les partagez pas.**

Pour récupérer cet URI à partir du portail Azure, procédez comme suit :

1.  Sélectionnez **Modules complémentaires**.  
    ![AddonsButton][]
2.  Recherchez le service MongoLab dans la liste de modules complémentaires.  
    ![MongolabEntry][]
3.  Pour accéder à la page du module, cliquez sur son nom.
4.  Cliquez sur **Informations de connexion**.  
    ![ConnectionInfoButton][]  
    L'URI MongoLab s’affiche :  
    ![ConnectionInfoScreen][]  
5.  Cliquez sur le bouton Presse-papiers à droite de la valeur MONGOLAB\_URI pour la copier entièrement vers le Presse-papiers.

  [Connexions]: http://www.mongodb.org/display/DOCS/Connections
  [AddonsButton]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
  [MongolabEntry]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
  [ConnectionInfoButton]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
