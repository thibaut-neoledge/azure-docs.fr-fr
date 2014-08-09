<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Remarque** : cet article permet de créer une machine virtuelle qui n'est pas connectée à un réseau virtuel. Si vous voulez que votre machine virtuelle utilise un réseau virtuel pour pouvoir vous connecter directement à vos machines virtuelles par nom d'hôte ou en configurant des connexions entre différents locaux, utilisez plutôt la méthode **From Gallery** et indiquez le réseau virtuel lorsque vous créez la machine virtuelle. Pour plus d'informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure][1].

Pour créer une machine virtuelle, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][2] en utilisant votre compte Azure.

2.  Dans le portail de gestion, dans le coin inférieur gauche de la page Web, cliquez sur **+New**, sur **Virtual Machine**, puis sur **From Gallery**. ![Créer une machine virtuelle](./media/create-and-configure-windows-server-2008-vm-in-portal/CreateWinVM.png)

3.  Sélectionnez une image de machine virtuelle Windows Server 2008 R2 SP1, puis cliquez sur la flèche Suivant dans le coin inférieur droit de la page.

4.  Sur la page **Virtual machine configuration**, fournissez les informations suivantes :

* Entrez le **Virtual Machine Name**, par exemple << testwinvm >>.
* Dans la zone **New User Name**, entrez << Administrateur >>.
* Dans la zone **New Password**, entrez un [mot de passe fort][3].
* Dans la zone **Confirm Password**, entrez de nouveau le mot de passe.
* Dans la liste déroulante, sélectionnez la **Size** adéquate.
  
  Cliquez sur la flèche Suivant pour continuer.

1.  Sur la page **Virtual machine mode**, entrez les informations suivantes :

* Sélectionnez **Standalone Virtual Machine**.
* Dans la zone **DNS Name**, entrez un sous-domaine valide au format **testwinvm.cloudapp.net**
* Dans la zone **Region/Affinity Group/Virtual Network**, sélectionnez une région d'hébergement pour cette image virtuelle.
  
  Cliquez sur la flèche Suivant pour continuer.

1.  Sur la page **Virtual machine options**, dans la zone **Availability Set** sélectionnez **(none)**. Cliquez sur la coche pour continuer.

2.  Patientez pendant qu'Azure prépare votre machine virtuelle.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/fr-fr/library/ms161962.aspx