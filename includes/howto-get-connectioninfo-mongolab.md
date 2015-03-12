Lors de l'approvisionnement d'une base de données MongoLab, MongoLab transmet un URI de connexion à Azure en utilisant le format de la chaîne de connexion standard de MongoDB. Cette valeur est utilisée pour initier une connexion MongoDB via le pilote MongoDB de votre choix. Pour plus d'informations sur les chaînes de connexion, consultez la page [Connexions](http://www.mongodb.org/display/DOCS/Connections) à l'adresse mongodb.org.

**Cet URI contient votre nom d'utilisateur et votre mot de passe pour la base de données.  Considérez ces informations comme confidentielles. Ne les partagez pas.**

Pour récupérer cet URI à partir du portail Azure, procédez comme suit :

1. Sélectionnez **Modules complémentaires**.  
![AddonsButton][button-addons]
1. Recherchez le service MongoLab dans la liste de modules.  
![MongolabEntry][entry-mongolabaddon]
1. Pour accéder à la page du module, cliquez sur son nom.
1. Cliquez sur **Informations de connexion**.  
![ConnectionInfoButton][button-connectioninfo]  
Votre URI MongoLab s'affiche :  
![ConnectionInfoScreen][screen-connectioninfo]  
1.  Cliquez sur le bouton Presse-papiers à droite de la valeur MONGOLAB_URI pour la copier entièrement vers le Presse-papiers.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
<!--HONumber=42-->
