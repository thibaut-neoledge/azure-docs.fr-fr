Un équilibreur de charge permet d’assurer une haute disponibilité pour vos charges de travail dans Azure. Un équilibreur de charge Azure est un équilibreur de charge de type Layer-4 (TCP, UDP) qui distribue le trafic entrant parmi les instances de service saines dans les services cloud ou les machines virtuelles définis dans un jeu d’équilibrage de la charge.
 
Vous pouvez configurer un équilibreur de charge pour :

- équilibrer la charge du trafic Internet entrant sur les machines virtuelles. L’équilibreur de charge auquel nous faisons référence dans ce scénario est un [équilibreur de charge accessible sur Internet](load-balancer-internet-overview.md) ;
- équilibrer le trafic entre des machines virtuelles dans un réseau virtuel (VNet), entre des machines virtuelles dans les services cloud ou entre des ordinateurs locaux et des machines virtuelles dans un réseau virtuel entre différents locaux. L’équilibreur de charge auquel nous faisons référence dans ce scénario est un [Équilibreur de charge interne (ILB)](load-balancer-internal-overview.md) ;
- 	transférer du trafic externe vers une instance spécifique de machine virtuelle.

<!---HONumber=Nov15_HO1-->