Certo, Rafael, vou te fazer um desenho simples mostrando o cenário que você está tentando montar e onde está o conflito.

* * *

**Cenário Atual (sem ajustes)**
-------------------------------

    [Internet]
        │
        │ (WAN - vmnic0)
        ▼
    [Servidor Windows Server RRAS - VM]
     NIC1 (WAN) → 172.16.33.2 / GW 172.16.33.1
     NIC2 (LAN) → 192.168.0.1  ← deseja usar esta rede
        │
        │ (vSwitch LAN - vmnic2)
        ▼
    [Port Group LAN no ESXi]
        │
        │  (Rede física ou interna do ESXi)
        ▼
    [Desktops / Outras VMs]
       → Atualmente estão na faixa **192.168.1.x**

**Problema:**  
O ESXi e/ou as VMs ligadas na LAN ainda estão configuradas para **192.168.1.x**, mas você quer que elas usem **192.168.0.x**.  
Isso quebra a comunicação com IPs diferentes de sub-rede.

* * *

**Cenário Corrigido (usando 192.168.0.x na LAN)**
-------------------------------------------------

    [Internet]
        │
        │ (WAN - vmnic0)
        ▼
    [Servidor Windows Server RRAS - VM]
     NIC1 (WAN) → 172.16.33.2 / GW 172.16.33.1
     NIC2 (LAN) → 192.168.0.1 (Gateway da rede interna)
        │
        │ (vSwitch LAN - vmnic2)
        ▼
    [Port Group LAN no ESXi]
        │
        │ (Rede física ou interna do ESXi)
        ▼
    [Desktops / Outras VMs]
       → 192.168.0.100~192.168.0.200 (via DHCP ou fixo)
          GW: 192.168.0.1
          DNS: 8.8.8.8 (ou outro)

* * *

📌 **O que muda para funcionar:**

1. No **ESXi**, no **Port Group da LAN**, garanta que todas as VMs e a NIC física da LAN estão **na mesma sub-rede** que você quer (192.168.0.x).

2. Configure DHCP no Windows Server para essa faixa, ou IP fixo nos desktops.

3. No RRAS, a NIC2 será “interface privada” e a NIC1 será “interface pública com NAT”.

* * *

Se quiser, posso te mandar também **o desenho mostrando onde ficam os vSwitch e Port Groups no ESXi** e como eles conectam nas NIC físicas, para você ver onde ligar cada rede.  
Isso ajuda muito a não confundir a WAN e a LAN.
