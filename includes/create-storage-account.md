## <a name="create-account"> </a>Création d'un compte Azure Storage

Pour utiliser Azure Storage, vous avez besoin d'un compte de stockage. Pour 
en créer un, procédez comme suit : (Vous pouvez également
créer un compte de stockage à l'aide de la bibliothèque cliente de gestion des services Azure ou de la gestion des services [REST API].)

1.  Connectez-vous au [portail de gestion Azure].

2.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

	![+new][plus-new]

3.  Cliquez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis sur **CRÉATION RAPIDE**.

	![Quick create dialog][quick-create-storage]

4.  Dans le champ URL, saisissez un nom de sous-domaine à utiliser dans l'URI du
    compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules
    et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI,
    utilisé pour adresser les ressources d'un objet Blob, file d'attente ou Table pour
    l'abonnement.

5.  Choisissez la région ou le groupe d'affinités où localiser le
    stockage. Si vous prévoyez d'utiliser le stockage à partir de votre
    application Azure, sélectionnez la région où vous déploierez votre
    application.

6. Vous pouvez éventuellement sélectionner le type de réplication désiré pour votre compte. La réplication géo-redondante, sélectionnée par défaut, assure la durabilité maximale. Pour plus de détails sur les options de réplication, consultez la page [Options de redondance d'Azure Storage](http://msdn.microsoft.com/fr-fr/library/azure/dn727290.aspx) et le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Cliquez sur **CRÉER UN COMPTE DE STOCKAGE**.

[Utilisation de l'API REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh264518.aspx
[Portail de gestion Azure]: http://manage.windowsazure.com
[plus-new]: ./media/create-storage-account/plus-new.png
[quick-create-storage]: ./media/create-storage-account/quick-storage-2.png
<!--HONumber=41-->
