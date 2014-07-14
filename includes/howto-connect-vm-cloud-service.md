<properties  authors="kathydav" editor="tysonn" manager="donaldg" />

# Connexion des machines virtuelles dans un service cloud

Lors de la création d'une machine virtuelle, un service cloud est automatiquement créé pour la contenir. Vous pouvez créer plusieurs machines virtuelles dans le même service cloud pour leur permettre de communiquer les unes avec les autres, pour équilibrer la charge du trafic réseau et pour garantir leur haute disponibilité.

Pour plus d'informations sur l'équilibrage de charge des machines virtuelles, consultez la page [Équilibrage de charge des machines virtuelles](../load-balancing-vms/). Pour plus d'informations sur la gestion de la disponibilité d'une application, consultez la page [Gestion de la disponibilité des machines
virtuelles](../manage-vm-availability/).

Dans un premier temps, vous devez créer une machine virtuelle et un service cloud. Vous pouvez ensuite connecter d'autres machines virtuelles à la première machine virtuelle dans le même service cloud.

1.  Créez une machine virtuelle en suivant la procédure indiquée à la page [Création d'une machine virtuelle personnalisée](../howto-custom-create-vm/).

2.  Après avoir créé la première machine virtuelle personnalisée, dans la barre de commandes du [portail de gestion][1], cliquez sur **New**.
    
    ![Création d’une machine virtuelle](./media/howto-connect-vm-cloud-service/Create.png)

3.  Cliquez sur **Virtual Machine**, puis sur **From Gallery**.
    
    ![Création d’une machine virtuelle personnalisée](./media/howto-connect-vm-cloud-service/CreateNew.png)
    
    La boîte de dialogue **Select the virtual machine operating system** s'affiche.

4.  Sur la page **Choose an image**, sélectionnez une image, puis cliquez sur la flèche pour continuer.
    
    La première page **Virtual machine configuration** s'affiche.

5.  Dans **Virtual Machine Name**, tapez le nom que vous souhaitez utiliser pour la machine virtuelle.

6.  Dans **Taille**, sélectionnez la taille à utiliser pour la machine virtuelle. Celle-ci dépend du nombre de cœurs nécessaires à votre application.

7.  Dans **New User Name**, tapez le nom du compte administratif que vous voulez utiliser pour gérer le serveur.

8.  Dans **New Password**, tapez un mot de passe fort pour le compte administratif. Dans **Confirm Password**, entrez de nouveau le mot de passe.

9.  Pour une machine virtuelle s'exécutant sous le système d'exploitation Linux, vous pouvez choisir de sécuriser la machine avec une clé SSH.

10. Dans **Cloud Service**, sélectionnez le service cloud dans lequel placer la nouvelle machine virtuelle.

11. Dans **Storage Account**, sélectionnez le compte de stockage du fichier .vhd ou laissez la valeur par défaut pour créer le compte de stockage automatiquement. Un seul compte de stockage est créé automatiquement. Toutes les autres machines virtuelles créées avec ce paramètre sont placées dans ce compte de stockage. Vous êtes limité à 20 comptes de stockage.

12. Pour utiliser un groupe à haute disponibilité, sélectionnez celui créé lors de la création de la première machine virtuelle.

13. Vérifiez la configuration par défaut du point de terminaison et modifiez-la si besoin.

14. Cliquez sur la coche pour créer la machine virtuelle connectée.



[1]: http://manage.windowsazure.com