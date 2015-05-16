## Création d'un compte Azure Storage

Pour utiliser Azure Storage, vous avez besoin d'un compte de stockage. Pour en créer un, procédez comme suit : Vous pouvez également créer un compte de stockage à l'aide de la bibliothèque cliente de gestion des services Azure ou de l’[API REST] de gestion des services.

1.  Connectez-vous au [portail de gestion Azure].

2.  En bas du volet de navigation, cliquez sur **NEW**.

	![\+nouveau][plus-new]

3.  Cliquez sur **DATA SERVICES**, sur **STORAGE**, puis sur **QUICK CREATE**.

	![Boîte de dialogue Création rapide][quick-create-storage]

4.  Dans URL, tapez un nom de sous-domaine à utiliser dans l’URI du compte de stockage. L’entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement.

5.  Choisissez la région ou le groupe d’affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

6. Vous pouvez éventuellement sélectionner le type de réplication désiré pour votre compte. La réplication géo-redondante, sélectionnée par défaut, assure la durabilité maximale. Pour plus de détails sur les options de réplication, consultez la page [Options de redondance d'Azure Storage](http://msdn.microsoft.com/library/azure/dn727290.aspx) et le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Cliquez sur **CREATE STORAGE ACCOUNT**.

[API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[portail de gestion Azure]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=52-->
