<properties  writer="josephd" editor="tysonn" manager="dongill" />

# Équilibrage de charge des machines virtuelles

Toutes les machines virtuelles créées dans Azure et appartenant au même service cloud ou réseau virtuel peuvent communiquer automatiquement entre elles à l'aide d'un canal réseau privé. Toutes les autres communications entrantes, telles que le trafic initié par des hôtes Internet ou des machines virtuelles dans d'autres services cloud ou réseaux virtuels, requièrent un point de terminaison.

Les points de terminaison servent plusieurs buts. Par défaut, les points de terminaison sur une machine virtuelle que vous créez via le portail de gestion Azure sont utilisés et configurés pour le trafic RDP (Remote Desktop Protocol) et de la session Windows PowerShell distante. Ces points de terminaison vous permettent d'administrer à distance la machine virtuelle sur Internet.

Les points de terminaison servent également à configurer l'équilibrage de charge Azure pour répartir un type de trafic spécifique entre plusieurs machines virtuelles ou services. Par exemple, vous pouvez répartir la charge du trafic des requêtes Web sur plusieurs serveurs ou rôles Web.

Chaque point de terminaison défini pour une machine virtuelle se voit attribuer un port public et un port privé (TCP ou UDP). Les hôtes Internet envoient le trafic entrant à l'adresse IP publique du service cloud et à un port public. Les machines virtuelles et services faisant partie du service cloud écoutent sur l'adresse IP privée et le port privé. L'équilibrage de charge mappe l'adresse IP publique et le numéro de port du trafic entrant à l'adresse IP privée et au numéro de port de la machine virtuelle (et inversement) pour le trafic de réponse de la machine virtuelle.

Lorsque vous configurez l'équilibrage de charge du trafic dans plusieurs machines virtuelles ou services, Azure fournit une distribution aléatoire du trafic entrant.

Pour un service cloud contenant des instances de rôles Web ou de rôles de travail, vous pouvez définir un point de terminaison public dans la définition de service. Pour un service cloud contenant des machines virtuelles, vous pouvez ajouter un point de terminaison à une machine virtuelle lors de sa création ou ultérieurement.

La figure suivante présente un point de terminaison à charge équilibrée pour le trafic Web (non chiffré) standard partagé entre trois machines virtuelles pour les ports public et privé 80. Celles-ci sont incluses dans un jeu d'équilibrage de la charge.

![équilibrage de la
charge](./media/load-balancing-vms/LoadBalancing.png)

Lorsque les clients Internet envoient des demandes de page Web à l'adresse IP publique du service cloud et au port TCP 80, l'équilibrage de charge effectue un équilibrage aléatoire de ces demandes entre les trois machines virtuelles du jeu d'équilibrage de la charge.

Pour créer un jeu d'équilibrage de la charge des machines virtuelles Azure, procédez comme suit :

* [Étape 1 : création de la première machine virtuelle](#firstmachine)
* [Étape 2 : création de machines virtuelles supplémentaires dans le
  même service cloud](#addmachines)
* [Étape 3 : création d'un jeu d'équilibrage de la charge avec la
  première machine virtuelle](#loadbalance)
* [Étape 4 : ajout de machines virtuelles au jeu d'équilibrage de
  charge](#addtoset)

## <a id="firstmachine"> </a>Étape 1 : création de la première machine virtuelle

Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure][1]. Pour créer la première machine virtuelle, vous pouvez utiliser la méthode À partir de la galerie ou Création rapide.

* **À partir de la galerie** : cette méthode vous permet de créer un point de terminaison et de spécifier un nom pour le service cloud créé en même temps que la machine virtuelle. Pour plus d'informations, consultez la rubrique [Création d'une machine virtuelle exécutant Linux](../virtual-machines-linux-tutorial) ou [Création d'une machine virtuelle exécutant Windows Server](../virtual-machines-windows-tutorial).

* **Création rapide** : créez une machine virtuelle en choisissant une image à partir de la galerie d'images et en fournissant des informations de base. Dans le cadre de cette méthode, vous devez ajouter un point de terminaison après avoir créé la machine virtuelle. Cette méthode crée également un service cloud utilisant un nom par défaut. Pour plus d'informations, consultez la rubrique [Création rapide d'une machine virtuelle](../virtual-machines-quick-create).

**Remarque** : une fois la machine virtuelle créée avec la méthode Création rapide, la page Cloud Services du portail de gestion répertorie le nom du service cloud ainsi que d'autres informations le concernant.

## <a id="addmachines"> </a>Étape 2 : création de machines virtuelles supplémentaires dans le même service cloud

À l'aide de la méthode À partir de la galerie, créez d'autres machines virtuelles dans le même service cloud que la première.

## <a id="loadbalance"> </a>Étape 3 : création d'un jeu d'équilibrage de la charge avec la première machine virtuelle

1.  Dans le portail de gestion Azure, cliquez sur **Machines virtuelles**, puis sur le nom de la première machine virtuelle.

2.  Cliquez sur **Points de terminaison**, puis sur **Ajouter**.

3.  Dans la page Ajouter un point de terminaison à la machine virtuelle, cliquez sur la flèche vers la droite.

4.  Dans la page Spécifier les détails du point de terminaison :
    * Dans **Nom**, tapez le nom du point de terminaison ou
      sélectionnez-en un dans la liste des points de terminaison
      prédéfinis pour les protocoles communs.
    * Dans **Protocole**, sélectionnez le protocole requis, TCP ou UDP,
      pour ce type de point de terminaison.
    * Dans **Port public** et **Port privé**, entrez les numéros de port
      dont se servira la machine virtuelle. Vous pouvez utiliser le port
      privé et des règles de pare-feu sur la machine virtuelle pour
      rediriger le trafic de façon pertinente pour votre application. Le
      port privé et le port public peuvent être identiques. Par exemple,
      pour un point de terminaison pour le trafic Web (HTTP), vous
      pouvez attribuer le port 80 comme port public ou privé.
<p></p>
5.  Sélectionnez **Créer un jeu d'équilibrage de la charge**, puis cliquez sur la flèche vers la droite.

6.  Sur la page Configurer le jeu d'équilibrage de la charge, indiquez le nom du jeu d'équilibrage de charge et attribuez les valeurs correspondant au comportement de sonde de l'équilibrage de charge Azure. L'équilibrage de la charge utilise des sondes pour déterminer si les machines virtuelles du jeu d'équilibrage de la charge sont en mesure de recevoir le trafic entrant.

7.  Cliquez sur la coche pour créer le point de terminaison à charge équilibrée. **Oui** s'affiche dans la colonne **Nom du jeu d'équilibrage de la charge** de la page **Points de terminaison** de la machine virtuelle.

## <a id="addtoset"> </a>Étape 4 : ajout de machines virtuelles au jeu d'équilibrage de charge

Après avoir créé le jeu d'équilibrage de charge, ajoutez-y les autres machines virtuelles. Pour chaque machine virtuelle du même service cloud :

1.  Dans le portail de gestion, cliquez sur **Machines virtuelles**, sur le nom de la nouvelle machine virtuelle, sur **Points de terminaison**, puis sur **Ajouter**.

2.  Sur la page Ajouter un point de terminaison à la machine virtuelle, cliquez sur **Ajouter un point de terminaison à un jeu d'équilibrage de la charge existant**, sélectionnez le nom du jeu d'équilibrage de la charge, puis cliquez sur la flèche vers la droite.

3.  Sur la page Spécifier les détails du point de terminaison, tapez le nom du point de terminaison, puis cliquez sur la coche.

<!-- LINKS -->



[1]: http://manage.windowsazure.com