Para alterar a rede de privada para pública em um Windows Server 2012, você pode usar o PowerShell. Primeiro, descubra a localização da rede e o índice da interface de rede usando o comando `Get-NetConnectionProfile`. Em seguida, se a categoria de rede for "Public", você pode alterá-la para "Private" usando `Set-NetConnectionProfile -InterfaceIndex <índice> -NetworkCategory Private`, onde `<índice>` é o número obtido no passo anterior. Por fim, verifique a mudança com `Get-NetConnectionProfile` novamente. 

Passos detalhados:

1. **Abra o PowerShell como administrador:**
   * Clique com o botão direito no ícone do Windows no canto inferior esquerdo da tela.
   * Selecione "Windows PowerShell (Admin)".
2. **Descubra a localização da rede e o índice da interface:**
   * Execute o seguinte comando: 

Código
         Get-NetConnectionProfile

* Anote o índice da interface (Network Adapter ID) e a categoria da rede (NetworkCategory). 
1. **1.** **Verifique se a rede está como pública:**
   
   * Se a saída do comando anterior mostrar `NetworkCategory : Public`, a rede está configurada como pública. 
* **2.** **Altere a rede para privada (se necessário):**

* Execute o seguinte comando, substituindo `<índice>` pelo número obtido no passo 2:

Código
         Set-NetConnectionProfile -InterfaceIndex <índice> -NetworkCategory Private

* Exemplo: `Set-NetConnectionProfile -InterfaceIndex 6 -NetworkCategory Private`. 
1. **Verifique a mudança:**
   * Execute novamente o comando `Get-NetConnectionProfile` para confirmar que a categoria da rede foi alterada para "Private". 

Observações:

* Certifique-se de que o serviço NLA (Network Location Awareness) esteja em execução e definido como automático, pois ele ajuda o sistema a identificar corretamente o perfil de rede.
* Você pode verificar o serviço NLA acessando "services.msc" e procurando pelo serviço "Reconhecimento de Local de Rede" (Network Location Awareness).
