Tout d'abord, vous utilisez l'Assistant Ajouter une notification
d'émission dans Visual Studio 2013 pour inscrire votre application
auprès du Windows Store, configurer votre service mobile pour activer
les notifications Push et ajouter du code à votre application pour
inscrire un canal d'appareil.

1.  Si vous ne l'avez pas encore fait, suivez cette procédure dans
    [Importer votre fichier publishsettings dans Visual
    Studio 2013](/en-us/documentation/articles/mobile-services-windows-how-to-import-publishsettings/)
    pour importer votre fichier publisher.settings dans Visual Studio.
    
    Cette procédure n'est pas nécessaire si vous avez déjà utilisé
    Visual Studio pour créer ou gérer des services mobiles dans votre
    abonnement Azure.

2.  Dans Visual Studio 2013, ouvrez l'Explorateur de solutions, cliquez
    avec le bouton droit sur le projet, cliquez sur **Ajouter**, puis
    sur **Notification Push...**.
    
    ![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)
    
    L'Assistant Ajouter une notification d'émission démarre.

3.  Cliquez sur **Suivant**, connectez-vous à votre compte Windows
    Store, puis indiquez un nom dans **Réserver un nouveau nom** et
    cliquez sur **Réserver**.
    
    ![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png)
    
    Une nouvelle inscription d'application est créée.

4.  Cliquez sur la nouvelle inscription dans la liste **Nom de
    l'application**, puis cliquez sur **Suivant**.
    
    ![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

5.  Dans la boîte de dialogue **Sélectionner un service**, cliquez sur
    le nom du service mobile que vous avez créé lorsque vous avez suivi
    le didacticiel [Prise en main de Mobile
    Services](/en-us/develop/mobile/tutorials/get-started/) ou [Prise en
    main des
    données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/),
    puis cliquez sur **Suivant** et sur **Terminer**.
    
    Le service mobile est mis à jour pour inscrire votre SID de package
    d'applications et votre clé secrète client ; une nouvelle table
    **channels** est créée. Mobile Services est maintenant configuré
    pour utiliser Windows Push Notification Services (WNS) afin de
    pouvoir envoyer des notifications vers votre application.



**Remarque**

Lorsque votre application n'est pas déjà configurée pour se
connecter au service mobile, l'Assistant effectue également les
mêmes tâches de configuration que celle indiquées dans <strong>Prise en
main des données.</strong> 




<!-- URLs. -->

