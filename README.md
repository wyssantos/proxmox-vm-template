## Criando uma VM no Proxmox a partir de um Template com Cloud-Init (Clone Full)

### 1. Acessar a Interface Web do Proxmox

1. Abra o seu navegador e vá até a interface web do Proxmox (`https://<IP_DO_PROXMOX>:8006`).
2. Faça login com suas credenciais de administrador.

### 2. Navegar até o Template

1. No painel à esquerda, clique no **datacenter** onde seu template `template-ubuntu2204` está armazenado.

### 3. Clonar a VM usando "Clone Full"

1. Clique com o botão direito no template **template-ubuntu2204**.
2. Selecione a opção **Clone**.
3. Na janela de clonagem, siga as etapas:

#### 3.1. Definir Nome, ID e Tipo de Clone

- **Name**: Insira `vm-ubuntu22` (nome da nova VM).
- **VM ID**: Insira `201` (ID da nova VM).
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
- **DNS domain**: Defina o nome do domínio para a VM (ex: `vm-ubuntu.local`).
- **DNS servers**: Defina os nomes dos nameservers do domínio para a VM (ex: `8.8.8.8 1.1.1.1`).
- **SSH public key**: Insira a chave ssh para logar na VM (você pode colar ou carregar a partir de um arquivo).
- **Upgrade packages**: Defina se deseja que o servidor seja atualizado automaticamente pelo cloud-init na inicialização.
- **IP Configuration**: Configure a rede para a VM (você pode escolher um IP estático ou usar DHCP).

   **Exemplo de configuração de rede (para IP estático)**:
   - **IPv4**: `192.168.1.10/24`
   - **Gateway**: `192.168.1.1`

   **Ou, se você preferir usar DHCP**, deixe a configuração de IP em branco, e o Proxmox atribuirá um IP automaticamente.

### 5. Ajustes de Hardware e Outras Configurações (Hardware)

Você pode fazer ajustes adicionais no hardware da VM:

- **CPU**: Defina o número de CPUs virtuais (quantidade de sockets e cores).
- **Memória**: Ajuste a quantidade de RAM conforme necessário (este valor é em megabytes).
- **Disco**: Caso deseje aumentar o tamanho do disco, informe o valor que deseja acrescentar em gigabytes (ex: `10`).

#### 5.1. Configurações Adicionais (Options)

- **Start at boot**: Configure se deseja que a VM inicie automaticamente em caso de reboot do servidor proxmox.
- **Protection**: Proteção pra evitar que a VM seja removida acidentalmente.

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

Agora você tem uma nova VM chamada `vm-ubuntu22`, clonada do template `template-ubuntu2204`, configurada para usar o **Cloud-Init** para personalizar automaticamente a configuração da máquina na inicialização, como IP, hostname, usuário e senha.

