La procédure suivante permet d'inscrire votre application auprès du Windows Store, de configurer votre service mobile pour activer les notifications Push et d'ajouter du code à votre application pour inscrire un canal d’appareil via votre concentrateur de notification. Visual Studio 2013 se connecte à Azure et à Windows Store à l'aide des informations d'identification que vous fournissez.

1. Dans Visual Studio 2013, ouvrez l’Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application Windows Store, cliquez sur **Ajouter**, puis sur **Notification Push...**. 

	![Assistant Ajouter une notification Push dans Visual Studio 2013](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	L’Assistant Ajouter une notification d’émission démarre.

2. Cliquez sur **Suivant**, connectez-vous à votre compte Windows Store, puis indiquez un nom dans **Réserver un nouveau nom** et cliquez sur **Réserver**.

	Une nouvelle inscription d’application est créée.

3. Cliquez sur la nouvelle inscription dans la liste **Nom de l’application**, puis cliquez sur **Suivant**.

4. Sur la page **Sélectionner un service**, cliquez sur le nom de votre service mobile, puis sur **Suivant** et **Terminer**.

	Le concentrateur de notification utilisé par votre service mobile est mis à jour via l'inscription à Windows Notification Services (WNS). Vous pouvez désormais utiliser Azure Notification Hubs pour envoyer des notifications de Mobile Services vers votre application via WNS.

	>[AZURE.NOTE]Ce didacticiel montre comment envoyer des notifications depuis un service mobile principal. Vous pouvez utiliser la même inscription au hub de notification pour envoyer des notifications depuis n’importe quel service principal. Pour plus d'informations, consultez la page [Vue d'ensemble des concentrateurs de notification](http://msdn.microsoft.com/library/azure/jj927170.aspx).

5. Une fois l'Assistant terminé, une nouvelle page **L'installation Push est presque terminée** s'ouvrira dans Visual Studio. Cette page présente une toute autre méthode permettant de configurer votre projet de service mobile pour envoyer des notifications.

	Le code qui est ajouté à votre solution d’application Windows universelle par l’Assistant Ajouter une Notification Push est spécifique à la plateforme. Plus loin dans cette rubrique, vous supprimerez cette redondance en partageant le code client Mobile Services, ce qui facilitera la maintenance de l’application universelle.

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet/
[Import your publishsettings file in Visual Studio 2013]: ../articles/mobile-services/mobile-services-windows-how-to-import-publishsettings.md

<!---HONumber=July15_HO4-->