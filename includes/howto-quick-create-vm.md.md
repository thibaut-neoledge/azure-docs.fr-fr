<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Création rapide d’une machine virtuelle

La méthode **Quick Create** permet de créer rapidement une machine virtuelle dans le portail de gestion. Lorsque vous créez cette machine, une seule boîte de dialogue vous invite à fournir les détails de configuration.

**Remarque** : cet article permet de créer une machine virtuelle qui n’est pas connectée à un réseau virtuel. Si vous souhaitez que la machine virtuelle utilise un réseau virtuel, utilisez plutôt la méthode **From Gallery** et spécifiez le réseau virtuel lors de la création de la machine virtuelle. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure][].

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans la barre de commandes, cliquez sur **New**.

    ![Création d’une machine virtuelle][]

3.  Cliquez sur **Virtual Machines**, puis sur **Quick Create**.

    ![Créer rapidement une machine virtuelle][]

    La boîte de dialogue **Create a New Virtual Machine** s’affiche.

4.  Pour la nouvelle machine virtuelle, saisissez les informations suivantes :

    -   **Nom DNS** : nom utilisé par la machine virtuelle créée et par le service cloud qui la contient.
    -   **Image** : image de plateforme utilisée pour créer la machine virtuelle.
    -   **Nom d’utilisateur** : nom du compte à utiliser pour gérer la machine virtuelle.
    -   **Mot de passe du compte** : saisissez et confirmez un mot de passe fort pour le compte.
    -   **Emplacement** : région contenant la machine virtuelle.

5.  Cliquez sur la coche pour créer la machine virtuelle.

    **Remarque :** un compte de stockage contenant la machine virtuelle est automatiquement créé.

    La nouvelle machine virtuelle est répertoriée sur la page **Virtual Machines**.

    ![Machine virtuelle correctement créée][]

  [Présentation du réseau virtuel Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Création d’une machine virtuelle]: ./media/howto-quick-create-vm/create.png
  [Créer rapidement une machine virtuelle]: ./media/howto-quick-create-vm/createquick.png
  [Machine virtuelle correctement créée]: ./media/howto-quick-create-vm/vmsuccesswindows.png
