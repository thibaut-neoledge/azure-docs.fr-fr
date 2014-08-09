
Après la propagation des enregistrements de votre nom de domaine, vous devez être en mesure d'utiliser votre navigateur pour vérifier que votre domaine personnalisé peut être utilisé pour accéder à votre site Web.

> [WACOM.NOTE] La propagation de l'enregistrement CNAME dans le système DNS peut prendre du temps. Vous pouvez utiliser un service tel que <http://www.digwebinterface.com/> pour vérifier que l'enregistrement CNAME est disponible.

Si vous n'avez pas déjà ajouté votre site Web comme point de terminaison de Traffic Manager, vous devez effectuer cette action pour faire fonctionner la résolution de nom, car le nom de domaine personnalisé assure l'acheminement vers Traffic Manager. Traffic Manager effectue ensuite l'acheminement vers votre site Web.. Utilisez les informations de [Ajout ou suppression de points de terminaison](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744839.aspx) pour ajouter votre site Web comme point de terminaison dans votre profil Traffic Manager.

> [WACOM.NOTE] Si votre site Web n'est pas répertorié lors de l'ajout d'un point de terminaison, vérifiez qu'il est configuré pour le mode Standard. Vous devez utiliser le mode Standard pour votre site Web si vous voulez utiliser Traffic Manager.

