# Configuração Raspberry

## Sistema Raspbian

### 1: Instalação do Raspbian
- Para realizar a instalação do raspbian é necessário acessar o site https://www.raspberrypi.org/downloads/ e realizar o download da ISO, conecte o Cartão MicroSD na máquina, realize a formatação com o **SD Formatter** e a instalação da ISO com o **Win32 Disk Imager**.

### 2: Configurar 
- Clique no menu iniciar do Raspbian, vá em **Preferences** e em **Raspberry Pi Configuration**;
- Em **Raspberry Pi Configuration**, selecione a aba **Interfaces**;
- Em **SSH**, selecione **enable** (utilizado para acesso ao sistema via SSH);
- Ao habilitar o acesso via SSH, o sistema irá recomendar a troca de senha do usuário, para isso só precisa acessar o terminal e digitar **passwd**;
- Na aba **Localisation**, podem ser configuradas as opções de localização, Timezone e linguagem de teclado;
- Para que todas estas configurações tenham efeito você deve reiniciar o sistema.

### 3: Configuração de IP Estático
- Normalmente o raspbian vem com o DHCP ligado, se na sua rede os dispositivos ficam na configuração estática, é necessário mudar as configurações em **/etc/dhcpcd.conf**, alterar para configurações de exemplo abaixo.

```
interface eth0
static ip_address=102.168.1.12/24
static routers=192.168.1.1
static domain_name_servers=8.8.8.8
```

### 4: Atualização de SO
- Rodar comandos abaixo:

```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get dist-update -y
sudo apt-get autoremove -y
```

### 5: Instalando requisitos do modo quiosque
- Rodar o comando abaixo:

```
sudo apt–get install –y chromium–browser ttf–mscorefonts–installer unclutter x11–xserver–utils
```

### 6: Configurar inicialização do SO
- Edite o arquivo autostart:

```
sudo nano .config/lxsession/LXDE–pi/autostart
```

- Preencha da seguinte maneira:

```
lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
@xscreensaver -no-splash
@point-rpi
@/usr/bin/chromium-browser --incognito --start-maximized --kiosk https://google.com.br/
@unclutter
@xset s off
@xset s noblank
@xset -dpms
```

*o arquivo autostart pode estar em lugar diferente, na dúvida, rode o comando ```find . -name "autostart"``` acesse os arquivos via nano e veja qual apresenta o mesmo começo do código acima.*

### 7: Configuração para inserção automática de Login
-

### 8:
-

### 9:
-

### 10:
-

### 11:
-

### 12:
-

### 13:
-
