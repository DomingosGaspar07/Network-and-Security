Cenário
Rede com três routers (R1, R2, R3), onde R1 e R3 estabelecem túneis GRE (Tunnel0 em IPv6 e Tunnel1 em IPv4) passando por R2, que atua como intermediário. O roteamento é feito com OSPFv2 (IPv4) e OSPFv3 (IPv6) simultaneamente (dual-stack).
R1 (vermelho)

Lo0: 192.168.1.0/24 e 2001:db8:acad:1::/64
Lo1: 172.16.1.0/24 e 2001:db8:acad:1721::/64
Interface e0/0 liga a R2 pela rede 10.1.2.0/24 e 2001:db8:acad:12::/64

R2 (preto, meio)

Interface e0/0 → liga a R1
Interface e0/1 → liga a R3 pela rede 10.2.3.0/24 e 2001:db8:acad:23::/64
Não participa dos túneis GRE diretamente, apenas encaminha o tráfego (transit router)

R3 (verde)

Lo0: 192.168.3.0/24 e 2001:db8:acad:3::/64
Lo1: 172.16.3.0/24 e 2001:db8:acad:1723::/64
Interface e0/0 liga a R2

Túneis GRE (entre R1 e R3, via R2)

Tunnel0: apenas IPv6, rede 2001:db8:ffff::/64
Tunnel1: apenas IPv4, rede 100.100.100.0/30

Rotas estáticas (underlay) — necessárias para o GRE subir
Em R1 (para alcançar R3 via R2):
ip route 192.168.3.0 255.255.255.0 10.1.2.2
ipv6 route 2001:db8:acad:3::/64 2001:db8:acad:12::2
Em R3 (para alcançar R1 via R2):
ip route 192.168.1.0 255.255.255.0 10.2.3.2
ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:23::2
Estas rotas garantem que os endereços de origem/destino dos túneis (Loopback 0 de R1 e R3) se alcancem mutuamente através de R2, mesmo antes do OSPF subir dentro dos túneis. R2 não precisa de rotas estáticas adicionais, pois já está diretamente conectado às duas redes (10.1.2.0/24 e 10.2.3.0/24).
Objetivo típico deste laboratório
Fazer R1 e R3 "enxergarem-se" diretamente via túnel GRE (simulando adjacência direta), rodando OSPF sobre os túneis, enquanto R2 apenas encaminha o tráfego encapsulado sem participar da topologia OSPF dos túneis (ou participando só na parte física, dependendo do desenho do lab).