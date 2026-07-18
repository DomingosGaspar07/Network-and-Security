OSPF with Authentication — HMAC-SHA-256

Lab prático de OSPF com autenticação HMAC-SHA-256,
garantindo que apenas routers autorizados formam
adjacências e trocam rotas na rede.


interface ethernet0/0
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 Cisco123
ip address 10.1.1.1 255.255.255.252
!
interface ethernet0/1
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 Cisco123
ip address 10.2.2.1 255.255.255.252
!
router ospf 1
area 0 authentication message-digest
network 10.1.1.0 0.0.0.3 area 0
network 10.2.2.0 0.0.0.3 area 0

### R3
interface ethernet0/0
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 Cisco123
ip address 10.1.1.2 255.255.255.252
!
router ospf 1
area 0 authentication message-digest
network 10.1.1.0 0.0.0.3 area 0
network 192.168.0.0 0.0.0.255 area 0

R4
interface ethernet0/0
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 Cisco123
ip address 10.2.2.2 255.255.255.252
!
router ospf 1
area 0 authentication message-digest
network 10.2.2.0 0.0.0.3 area 0
network 192.168.1.0 0.0.0.255 area 0

---
 Verificação
! Verificar adjacências OSPF
show ip ospf neighbor
! Verificar autenticação activa por interface
show ip ospf interface ethernet0/0
! Verificar tabela de routing
show ip route ospf
! Testar conectividade
ping 192.168.1.1 source 192.168.0.1



Por que HMAC-SHA-256?

| Método         | Segurança | Estado        |
|----------------|-----------|---------------|
| Sem auth       | ❌ Nenhuma | Inseguro      |
| Texto simples  | ❌ Fraca   | Obsoleto      |
| MD5            | ⚠️ Média  | Desaconselhado|
| HMAC-SHA-256   | ✅ Forte   | Recomendado   |

O HMAC-SHA-256 garante:
- ✅ Integridade dos pacotes OSPF
- ✅ Autenticação dos routers vizinhos
- ✅ Protecção contra injecção de rotas falsas
- ✅ Resistência a ataques de força bruta



 Ataques Prevenidos

- **Rogue Router** — router não autorizado
  não consegue formar adjacência OSPF
- **Route Injection** — rotas falsas são rejeitadas
- **Man-in-the-Middle** — pacotes alterados
  são descartados pelo HMAC
- **Replay Attack** — pacotes OSPF capturados
  e reenviados são detectados e rejeitados
