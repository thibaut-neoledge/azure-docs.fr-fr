Une fois l’écouteur de groupe de disponibilité créé, il peut être nécessaire d’ajuster les paramètres de cluster **RegisterAllProvidersIP** et **HostRecordTTL** pour la ressource de l’écouteur.  Ces paramètres peuvent réduire le délai de reconnexion après un basculement, ce qui peut empêcher des délais d'expiration de la connexion. Pour plus d'informations sur ces paramètres et voir des exemples de code, consultez la rubrique [Créer ou configurer un écouteur de groupe de disponibilité](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).



<!--HONumber=Nov16_HO3-->


