Rafael, esse erro de **"tempo limite expirado" ao entrar no domínio** quase sempre está ligado a **DNS incorreto ou indisponível** — mesmo que o nome do domínio e o NetBIOS estejam corretos.

No caso de um Windows Server **controlador de domínio (AD)**, o DNS tem um papel **central**:

* O cliente que vai entrar no domínio **precisa** consultar o DNS do **próprio servidor AD** (não pode ser 8.8.8.8 ou outro DNS externo).

* Se o cliente não usa o DNS interno, ele não consegue localizar o **SRV record** do AD e o join falha com “tempo limite”.

* * *

1️⃣ Onde corrigir
-----------------

### **No servidor Windows Server (AD + DNS)**

* A NIC **LAN** (192.168.0.2 no seu caso) deve ter como **DNS preferencial**:
  
      192.168.0.2 (o próprio servidor)

* Como **DNS alternativo** (opcional):
  
      vazio ou 8.8.8.8 (para consultas externas)

* O **Serviço DNS** deve estar instalado e configurado para:
  
  * Ter a **zona do domínio** (ex.: `empresa.local`).
  
  * Ter o **registro A** do controlador (`DC1.empresa.local`) apontando para 192.168.0.2.
  
  * Ter os **registros SRV** gerados automaticamente pelo AD.

* * *

### **Nos desktops que vão entrar no domínio**

* **IP fixo** ou DHCP, mas **DNS tem que ser 192.168.0.2** (o servidor).  
  Se estiver pegando DNS de fora, o join vai falhar.

* Teste no desktop:
  
      nslookup dominio.local
      nslookup _ldap._tcp.dc._msdcs.dominio.local

  Ambos precisam responder com o IP do seu servidor AD.

* * *

2️⃣ Passos para entrar no domínio
---------------------------------

1. Garantir que o desktop **consegue pingar** o servidor AD pelo nome e pelo FQDN:
      ping 192.168.0.2
      ping servidor.dominio.local

2. Garantir que o **hora/data** do desktop está igual à do servidor (desvio >5min bloqueia autenticação Kerberos).

3. No desktop:
   
   * Propriedades do sistema → Nome do computador → Alterar → Membro de domínio → Digitar `dominio.local`.

4. Ao pedir credenciais, usar:
      DOMÍNIO\Administrador

5. Se tudo certo, vai dar mensagem de boas-vindas e pedir para reiniciar.

* * *

📌 **Resumo do erro no seu caso**:  
O "tempo limite expirado" normalmente significa que o cliente não conseguiu **resolver o nome do domínio no DNS interno**.  
Mesmo que o IP esteja correto, se o DNS estiver apontando para Google, Cloudflare, roteador, etc., ele nunca vai achar os registros SRV do AD.

* * *

Aí está o ponto crítico, Rafael — **o DNS do AD não deve responder pela interface WAN**.  
Se as duas interfaces (WAN e LAN) estão marcadas nas propriedades do DNS, o Windows Server está tentando atender requisições também pelo IP da WAN (DHCP), e isso causa dois problemas graves:

1. **Registros SRV e A do domínio** acabam sendo publicados com o IP errado (da WAN).

2. Os clientes internos (192.168.0.x) podem receber respostas erradas ou o AD pode tentar autenticar pelo IP da WAN, que não é roteável internamente.

* * *

🔹 Como corrigir isso no DNS do Windows Server
----------------------------------------------

1. Abra o **Gerenciador DNS**.

2. Clique com o botão direito no nome do servidor DNS → **Propriedades**.

3. Aba **Interfaces**:
   
   * **Desmarque a WAN** (172.16.x.x que veio via DHCP).
   
   * **Deixe marcado apenas o IP da LAN** (192.168.0.2).
   
   * Isso garante que o DNS só responde na rede interna.

4. Aba **Encaminhadores**:
   
   * Adicione aqui um DNS externo (8.8.8.8 ou o DNS do seu provedor).
   
   * Assim, o servidor ainda consegue resolver sites externos, mas os clientes internos usam apenas ele como DNS.

5. **Reinicie o serviço DNS**:
      net stop dns
      net start dns

  ou no Services.msc.

* * *

🔹 Depois da correção
---------------------

* No prompt do servidor, rode:
  
      ipconfig /registerdns

* Depois, no desktop:
  
      ipconfig /flushdns
      ipconfig /registerdns

* Teste no desktop:
  
      nslookup dominio.local
      nslookup _ldap._tcp.dc._msdcs.dominio.local

  Ambos precisam apontar para **192.168.0.2**.

* * *

📌 Importante:  
No **Adaptador WAN** (172.16.x.x) do Windows Server, também entre em **Propriedades TCP/IP v4** e **remova qualquer entrada de DNS**.  
Só o adaptador LAN (192.168.0.2) deve ter o DNS **192.168.0.2**.

* * *

Se quiser, eu posso te passar um **checklist rápido de 5 passos** que uso para resolver 100% dos erros de “tempo limite ao entrar no domínio” nesses cenários com duas NICs.  
Acho que no seu caso, resolveria na primeira tentativa.
