# Manual Prático de LVM (Logical Volume Manager)
 O LVM (Logical Volume Manager) é uma ferramenta poderosa no Linux que permite gerenciar
 volumes lógicos de forma flexível e eficiente. Este guia oferece um passo a passo prático para
 entender, criar, redimensionar, adicionar e remover volumes lógicos usando LVM.
 ## 1. Conceitos Básicos
 Antes de começar, é importante entender os componentes principais do LVM:- **Physical Volume (PV)**: Partição ou disco físico que será usado pelo LVM.- **Volume Group (VG)**: Grupo que combina um ou mais PVs.- **Logical Volume (LV)**: Volumes criados dentro de um VG, que podem ser formatados e
 montados como sistemas de arquivos.--
## 2. Preparar o Ambiente
 ### Passo 1: Listar discos disponíveis
 ```bash
 lsblk
 ```
 > Este comando lista todos os discos e partições. Identifique o disco que deseja usar.
 ### Passo 2: Criar uma partição para LVM
1. Abra o utilitário de particionamento:
   ```bash
   fdisk /dev/sdX
   ```
   Substitua `/dev/sdX` pelo disco alvo.
 2. Crie uma nova partição:
   - Pressione `n` para nova partição.
   - Escolha o tipo de partição (primária).
   - Aceite os valores padrão ou ajuste conforme necessário.
 3. Alterar o tipo para LVM:
   - Pressione `t` e escolha o tipo `8e` (Linux LVM).
 4. Salve e saia:
   - Pressione `w` para salvar as alterações.--
## 3. Criar Physical Volume (PV)
 ```bash
 pvcreate /dev/sdX1
 ```
 > Substitua `/dev/sdX1` pela partição criada. Este comando inicializa a partição para uso com LVM.
 Verifique o PV:
 ```bash
 pvdisplay
 ```
--
## 4. Criar Volume Group (VG)
 ```bash
 vgcreate meu_vg /dev/sdX1
 ```
 > Cria um Volume Group chamado `meu_vg` usando o PV especificado.
 Verifique o VG:
 ```bash
 vgdisplay
 ```--
## 5. Criar Logical Volume (LV)
 ### Criar um LV de tamanho especificado (exemplo: 10G):
 ```bash
 lvcreate -L 10G -n meu_lv meu_vg
 ```
 > Cria um Logical Volume chamado `meu_lv` de 10GB dentro do VG `meu_vg`.
 Verifique o LV:
 ```bash
 lvdisplay
 ```
### Formatar o LV:
 ```bash
 mkfs.ext4 /dev/meu_vg/meu_lv
 ```
 > Formata o LV com o sistema de arquivos ext4.
 ### Montar o LV:
 1. Crie um ponto de montagem:
   ```bash
   mkdir /mnt/meu_lv
   ```
 2. Monte o LV:
   ```bash
   mount /dev/meu_vg/meu_lv /mnt/meu_lv
   ```
 Verifique o ponto de montagem:
 ```bash
 df -h
 ```--
## 6. Redimensionar Logical Volume
 ### Expandir o LV:
 1. Redimensionar o LV (exemplo: aumentar para 20G):
   ```bash
   lvextend -L 20G /dev/meu_vg/meu_lv
   ```
 2. Redimensionar o sistema de arquivos:
   ```bash
   resize2fs /dev/meu_vg/meu_lv
   ```
 ### Reduzir o LV:
 1. Desmonte o LV:
   ```bash
   umount /mnt/meu_lv
   ```
 2. Verifique o sistema de arquivos:
   ```bash
   e2fsck -f /dev/meu_vg/meu_lv
   ```
 3. Reduza o sistema de arquivos (exemplo: para 5G):
   ```bash
   resize2fs /dev/meu_vg/meu_lv 5G
   ```
 4. Reduza o LV:
   ```bash
   lvreduce -L 5G /dev/meu_vg/meu_lv
   ```
 5. Monte novamente:
   ```bash
   mount /dev/meu_vg/meu_lv /mnt/meu_lv
   ```--
## 7. Adicionar Novo Disco ao Volume Group
 1. Crie um PV no novo disco:
   ```bash
   pvcreate /dev/sdY1
   ```
 2. Adicione o PV ao VG:
   ```bash
   vgextend meu_vg /dev/sdY1
   ```--
## 8. Remover Logical Volume
 1. Desmonte o LV:
   ```bash
   umount /mnt/meu_lv
   ```
 2. Remova o LV:
   ```bash
   lvremove /dev/meu_vg/meu_lv
   ```
--
## 9. Remover Volume Group
 1. Certifique-se de que todos os LVs foram removidos.
 2. Remova o VG:
   ```bash
   vgremove meu_vg
   ```--
## 10. Remover Physical Volume
 ```bash
 pvremove /dev/sdX1
 ```
 > Isso remove as informações de LVM da partição.--
Com esses passos, você agora pode gerenciar LVM de forma eficiente. Cada comando é
 acompanhado de explicações e exemplos para facilitar o aprendizado.
