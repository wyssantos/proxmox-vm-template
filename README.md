## Criando uma VM no Proxmox a partir de um Template com Cloud-Init (Clone Full)

### 1. Acessar a Interface Web do Proxmox

1. Abra o seu navegador e vá até a interface web do Proxmox (`https://<IP_DO_PROXMOX>:8006`).
2. Faça login com suas credenciais de administrador.

### 2. Navegar até o Template

1. No painel à esquerda, clique no **datacenter** onde seu template `temp-ubuntu22` está armazenado.
2. Em seguida, clique na **armazenagem** que contém o template (por exemplo, `local`, `local-lvm`, etc.).
3. Verifique se o template `temp-ubuntu22` está listado na seção de **Templates** dentro do armazenamento.

### 3. Clonar a VM usando "Clone Full"

1. Clique com o botão direito no template **temp-ubuntu22**.
2. Selecione a opção **Clone**.
3. Na janela de clonagem, siga as etapas:

#### 3.1. Definir Nome e Tipo de Clone

- **Name**: Insira `vm-ubuntu22` (nome da nova VM).
- **Clone Mode**: Selecione a opção **Full Clone**. Isso criará uma cópia completa do template, permitindo edições independentes para a nova VM.

#### 3.2. Opções Adicionais

- **Target**: Escolha o **node** (servidor físico) onde a VM será criada.
- **Storage**: Defina o armazenamento de disco que será utilizado para a nova VM.
- **Mac Address**: O Proxmox vai gerar um novo endereço MAC automaticamente.

Clique em **Clone** para iniciar o processo de clonagem.

### 4. Configurar Cloud-Init para a Nova VM

Após o clone ser completado, você precisa configurar as opções do **Cloud-Init** para personalizar a VM.

1. Após o clone ser criado, clique na nova VM `vm-ubuntu22` na lista de VMs.
2. No painel da nova VM, vá para a aba **Cloud-Init**.

#### 4.1. Ajustar a Configuração do Cloud-Init

Na aba **Cloud-Init**, você pode configurar diversos parâmetros de inicialização para a VM, como:

- **User**: O nome de usuário do sistema (por exemplo, `ubuntu`).
- **Password**: Defina uma senha para o usuário.
- **SSH Keys**: Se necessário, adicione uma chave SSH para acesso remoto.
- **Hostname**: Defina o nome do host para a VM (ex: `vm-ubuntu22`).
- **IP Configuration**: Configure a rede para a VM (você pode escolher um IP estático ou usar DHCP).

   **Exemplo de configuração de rede (para IP estático)**:
   - **IPv4**: `192.168.1.10`
   - **Netmask**: `255.255.255.0`
   - **Gateway**: `192.168.1.1`

   **Ou, se você preferir usar DHCP**, deixe a configuração de IP em branco, e o Proxmox atribuirá um IP automaticamente.

#### 4.2. Configurações Adicionais

- **Timezone**: Configure o fuso horário, se necessário (ex: `America/Sao_Paulo`).
- **DNS Servers**: Adicione servidores DNS personalizados se necessário (por exemplo, `8.8.8.8` para o Google DNS).

### 5. Ajustes de Hardware e Outras Configurações

Você pode fazer ajustes adicionais no hardware da VM:

- **CPU**: Defina o número de CPUs virtuais.
- **Memória**: Ajuste a quantidade de RAM conforme necessário.
- **Disco**: Caso deseje aumentar ou diminuir o tamanho do disco, é possível fazer isso na aba **Hardware**.

### 6. Iniciar a VM

1. Após configurar o Cloud-Init e o hardware da VM, clique na aba **Start** para iniciar a VM.
2. O Proxmox irá iniciar a VM e o Cloud-Init será executado durante o boot para configurar a rede, o hostname, o usuário, e outras opções.

### 7. Acessar a VM via Console ou SSH

Após a VM ser inicializada, você pode acessar o console da VM diretamente pela interface web do Proxmox:

1. Na nova VM `vm-ubuntu22`, clique na aba **Console** para acessar o terminal da VM diretamente.
2. Se configurou as chaves SSH no Cloud-Init, também pode acessar a VM via SSH.

### 8. Verificar se o Cloud-Init foi executado corretamente

Se você configurou o Cloud-Init para atribuir um IP estático ou DHCP, verifique as configurações de rede da VM após ela ser iniciada.

### Conclusão

Agora você tem uma nova VM chamada `vm-ubuntu22`, clonada do template `temp-ubuntu22`, configurada para usar o **Cloud-Init** para personalizar automaticamente a configuração da máquina na inicialização, como IP, hostname, usuário e senha.

