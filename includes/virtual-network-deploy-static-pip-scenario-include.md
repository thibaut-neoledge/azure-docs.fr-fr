## Scénario

Ce document vous guide tout au long des étapes d’un déploiement dans lequel une adresse IP publique statique est allouée à une machine virtuelle. Ce scénario comprend une seule machine virtuelle qui possède sa propre adresse IP publique. La machine virtuelle, qui fait partie d’un sous-réseau nommé **FrontEnd**, dispose également d’une adresse IP privée statique (**192.168.1.101**) dans ce sous-réseau.

Il peut être nécessaire de disposer d’une adresse IP statique pour les serveurs web qui exigent des connexions SSL dans lesquelles le certificat SSL est lié à une adresse IP.

![DESCRIPTION DE L'IMAGE](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Pour déployer l’environnement représenté dans la figure ci-dessus, effectuez les étapes ci-dessous.

<!---HONumber=AcomDC_0114_2016-->