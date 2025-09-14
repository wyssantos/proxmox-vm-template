# Criar Templates a partir de Cloud Images no Proxmox

Este guia descreve como criar templates a partir de imagens de nuvem no Proxmox, utilizando como exemplo, uma imagem do Ubuntu 22.04 Jammy Server.

## Pré-requisitos

- Proxmox instalado e configurado.
- Acesso ao servidor Proxmox via SSH.
- Imagem de nuvem do Ubuntu 22.04 (ou outra distribuição compatível).

## Passo 1: Instalar Libguestfs Tools

Libguestfs é uma ferramenta que permite manipular discos de máquinas virtuais. Primeiro, instale o pacote necessário para poder personalizar a imagem de nuvem.

```bash
apt install libguestfs-tools -y
```

## Passo 2: Baixar a Imagem de Nuvem

Acesse o servidor Proxmox e vá até o diretório onde as imagens de máquina virtual são armazenadas.

```bash
cd /var/lib/vz/images
```

Em seguida, baixe a imagem de nuvem do Ubuntu 22.04 (Jammy Server).

```bash
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
```

## Passo 3: Adicionar o QEMU Guest Agent

O QEMU Guest Agent é uma ferramenta que permite a interação entre o host Proxmox e as máquinas virtuais. Para instalar o QEMU Guest Agent na imagem de nuvem, utilize o `virt-customize`.

```bash
virt-customize --add jammy-server-cloudimg-amd64.img --install qemu-guest-agent
```

## Passo 4: Criar a Máquina Virtual (VM) para o Template

Agora, crie a VM que servirá como base para o template. O número da VM será `9999` neste exemplo, mas você pode escolher outro número.

```bash
qm create 9999 --name template-ubuntu22 --numa 1 --ostype l26 --cpu cputype=host --sockets 1 --cores 2 --memory 2048 --net0 virtio,bridge=vmbr0
```

### Explicação dos parâmetros:
- `--numa 0`: Não habilitar NUMA (Non-Uniform Memory Access).
- `--ostype l26`: Define o tipo de sistema operacional como Linux.
- `--cpu cputype=host`: Usa o tipo de CPU do host.
- `--sockets 1 --cores 2`: Define 1 soquete e 2 núcleos de CPU.
- `--memory 2048`: Atribui 2 GB de memória RAM.
- `--net0 virtio,bridge=vmbr0`: Configura a rede usando o driver `virtio` e a bridge `vmbr0`.

## Passo 5: Importar o Disco da Imagem na VM

Importe o disco da imagem que você baixou para a VM criada.

```bash
qm importdisk 9999 /var/lib/vz/jammy-server-cloudimg-amd64.img local-lvm
```

Em seguida, adicione o disco importado à VM:

```bash
qm set 9999 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9999-disk-0
```

## Passo 6: Adicionar Disco de Cloud-Init

O Cloud-Init é uma ferramenta que permite configurar a VM automaticamente na primeira inicialização (exemplo: configurar rede, usuários, SSH). Para adicionar o disco de configuração do Cloud-Init, use:

```bash
qm set 9999 --ide2 local-lvm:cloudinit
```

## Passo 7: Configurar o Disco de Inicialização

Defina o disco de inicialização da VM para usar o disco SCSI (que contém o sistema operacional).

```bash
qm set 9999 --boot c --bootdisk scsi0
```

## Passo 8: Habilitar o Agente da VM

Habilite o QEMU Guest Agent na VM para permitir a comunicação entre o host e a máquina virtual.

```bash
qm set 9999 --agent enabled=1
```

## Passo 9: Converter a VM em Template

Após configurar a VM, converta-a em um template que poderá ser utilizado para criar novas VMs.

```bash
qm template 9999
```

## Usando o Template

Agora que você criou o template, pode utilizá-lo para criar novas máquinas virtuais com base nele. Para criar uma VM a partir do template, consulte o [HowTo](https://github.com/wyssantos/proxmox-vm-template) deste repositório.

---

### Observações:

- Certifique-se de ter espaço suficiente no armazenamento onde as imagens e VMs serão salvas.
- O Cloud-Init pode ser configurado para automatizar tarefas como o nome do host, configuração de rede e credenciais SSH. Para mais informações sobre como configurar o Cloud-Init, consulte a [documentação oficial](https://cloud-init.readthedocs.io/).

