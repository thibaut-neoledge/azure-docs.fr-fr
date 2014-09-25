Pour utiliser les opérations de stockage, vous avez besoin d'un compte de stockage Azure. Pour en créer un, procédez comme suit : (Vous pouvez également créer un compte de stockage [à l'aide de l'API REST][1].)

1.  Connectez-vous au [portail de gestion Azure][2].

2.  En bas du volet de navigation, cliquez sur **NEW**.
    
   	![+nouveau](./media/create-storage-account/plus-new.png)

3.  Cliquez sur **DATA SERVICES**, sur **STORAGE**, puis sur **QUICK CREATE**.
    
   	![Boîte de dialogue Création rapide](./media/create-storage-account/quick-storage-2.png)

4.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

5.  Choisissez la région ou le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

6.  Vous pouvez éventuellement activer les géo-réplications.

7.  Cliquez sur **CREATE STORAGE ACCOUNT**.



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
[2]: http://manage.windowsazure.com