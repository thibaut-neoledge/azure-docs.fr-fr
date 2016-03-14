#### Création d’un service

1.  À l’aide des informations d’identification de votre compte Microsoft, connectez-vous au portail Azure Classic en utilisant l’URL qui suit : [https://manage.windowsazure.com/](https://manage.windowsazure.com/)

2.  Dans le portail, cliquez sur **Nouveau > Services de données > StorSimple Manager > Création rapide**.

3.  Dans le formulaire qui s’affiche, procédez comme suit :

	1.  Indiquez un **nom** unique pour votre service. Il s’agit d’un nom convivial qui peut être utilisé pour identifier le service. Le nom peut comporter entre 2 et 50 caractères qui peuvent être des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre.

	2.  Pour permettre à un service de gérer un appareil virtuel StorSimple, dans le menu déroulant des **Types d’appareils gérés**, choisissez **Série d’appareils virtuels**.

	3.  Indiquez un **emplacement** pour votre service. L’emplacement fait référence à la région géographique dans laquelle vous souhaitez déployer votre appareil.

	 -   Si vous avez d’autres charges de travail dans Azure que vous souhaitez déployer avec votre appareil StorSimple, nous vous invitons à utiliser ce centre de données.

   	 -   Le gestionnaire StorSimple et le stockage Azure peuvent se trouver en deux emplacements différents. Dans ce cas, vous devez créer séparément le gestionnaire StorSimple et le compte de stockage Azure. Pour créer un compte de stockage Azure, accédez au service Azure Storage dans le portail et suivez les étapes indiquées dans [Créer un compte Azure Storage](storage-create-storage-account.md#create-a-storage-account). Une fois ce compte créé, ajoutez-le au service StorSimple Manager en suivant les étapes indiquées dans [Configurer un nouveau compte de stockage pour le service](#optional-step-configure-a-new-storage-account-for-the-service).
   	 
   	 	
	1.  Choisissez un **abonnement** dans la liste déroulante. L’abonnement est lié à votre compte de facturation. Ce champ n’est pas présent si vous n’avez qu’un seul abonnement.

	1.  Sélectionnez **Créer un compte de stockage Azure** pour créer automatiquement un compte de stockage avec le service. Ce compte de stockage possédera un nom spécial, tel que « storsimplebwv8c6dcnf ». Si vous avez besoin de vos données dans un autre emplacement, désactivez cette case à cocher.

	1.  Cliquez sur **Créer StorSimple Manager** pour créer le service.

		![](./media/storsimple-ova-create-new-service/image1-include.png)

	Vous allez être redirigé vers la page d’accueil **Service**. La création de service dure quelques minutes. Un message vous informe que le service a été créé.

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	Le statut du service sera alors **Actif**.

<!---HONumber=AcomDC_0302_2016-->