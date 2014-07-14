Lors de la mise en service d'une base de données MongoLab, MongoLab transmet un URI de connexion à Azure en utilisant le format de la chaîne de connexion standard de MongoDB. Cette valeur est utilisée pour initier une connexion MongoDB via le pilote MongoDB de votre choix. Pour plus d'informations sur les chaînes de connexion, consultez la page [Connexions][1] sur mongodb.org.

**Cet URI contient votre nom d'utilisateur et votre mot de passe pour la base de données. Considérez ces informations comme étant sensibles, ne les partagez pas.**

Pour récupérer cet URI à partir du portail Azure, procédez comme suit :

1.  Sélectionnez **Add-ons**.  
     ![BoutonModules](./media/howto-get-connectioninfo-mongolab/button-addons.png)
2.  Recherchez le service MongoLab dans la liste de modules.  
     ![EntréeMongoLab](./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png)
3.  Pour accéder à la page du module, cliquez sur son nom. 
4.  Cliquez sur **Connection Info**.
![BoutonInformationsDeConnexion](./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png)
    
     L'URI MongoLab s'affiche :
![ÉcranInformationsDeConnexion](./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png)
5.  Cliquez sur le bouton Presse-papiers à droite de la valeur
    MONGOLAB\_URI pour la copier entièrement vers le Presse-papiers.



[1]: http://www.mongodb.org/display/DOCS/Connections