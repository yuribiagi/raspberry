# Configuração Raspberry

## Sistema Raspbian

### 1: Instalação do Raspbian
- Para realizar a instalação do raspbian é necessário acessar o site https://www.raspberrypi.org/downloads/ e realizar o download da ISO, conecte o Cartão MicroSD na máquina, realize a formatação com o **[SD Formatter](https://www.sdcard.org/downloads/formatter/)** e a instalação da ISO com o **[Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/)**.

### 2: Configurar acesso SSH
- Clique no menu iniciar do Raspbian, vá em **Preferences** e em **Raspberry Pi Configuration**;
- Em **Raspberry Pi Configuration**, selecione a aba **Interfaces**;
- Em **SSH**, selecione **enable** (utilizado para acesso ao sistema via SSH);
- Ao habilitar o acesso via SSH, o sistema irá recomendar a troca de senha do usuário, para isso só precisa acessar o terminal e digitar **passwd**;
- Na aba **Localisation**, podem ser configuradas as opções de localização, Timezone e linguagem de teclado;
- Para que todas estas configurações tenham efeito você deve reiniciar o sistema.

### 3: Configurar acesso VNC
- Rodar comandos abaixo:

```
sudo apt update
sudo apt install realvnc-vnc-server realvnc-vnc-viewer
```

- Ativar o VNC:

```
sudo raspi-config
```

Navegue até a **Opções de interface** e habilite o **VNC para Sim**.

- Definindo senha:

```
nano /root/.vnc/config.d/vncserver-x11
```

Substitua **Authentication=SystemAuth** para **Authentication=VncAuth** e salve o arquivo.

- No terminal execute ```sudo vncpasswd -service``` vai ser solicitado a senha, defina e reinicie o servidor VNC.

### 4: Configuração de IP Estático
- Normalmente o raspbian vem com o DHCP ligado, se na sua rede os dispositivos ficam na configuração estática, é necessário mudar as configurações em **/etc/dhcpcd.conf**, alterar para configurações de exemplo abaixo.

```
interface eth0
static ip_address=102.168.1.12/24
static routers=192.168.1.1
static domain_name_servers=8.8.8.8
```

### 5: Atualização de SO
- Rodar comandos abaixo:

```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get dist-update -y
sudo apt-get autoremove -y
```

### 6: Instalando requisitos do modo quiosque
- Rodar o comando abaixo:

```
sudo apt–get install –y chromium–browser ttf–mscorefonts–installer unclutter x11–xserver–utils
```

### 7: Configurar inicialização do SO
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

### 8: Configuração para inserção automática de Login
- O xdotool é uma ferramenta de instruções que permite aos programadores emularem o pressionamento de teclas do teclado e do mouse, além de manipular janelas.

- Para instalar, acesse o terminal e digite o seguinte comando:

```sudo apt-get install xdotool```

- Crie um arquivo chamado logon.sh, através do seguinte comando no terminal:

```sudo nano /home/pi/logon.sh```

- No novo arquivo criado, digite as seguintes informações:

```
#!/usr/bash
sleep 60
username="usuario"
password="senha"
echo "Setting Display"
export DISPLAY=:0
echo "Sending Username"
for char in $(sed -E s/'(.)'/'\1 '/g <<<"$username"); do
 xdotool type --window chromium $char
done
 xdotool key --window chromium Tab
sudo echo "Sending Password"
for char in $(sed -E s/'(.)'/'\1 '/g <<<"$password"); do
 xdotool type --window chromium $char
done
 xdotool key --window chromium Return
echo "Waiting 5 Seconds"
sleep 5
echo "Setting Remember Password"
xdotool key --window chromium Tab
xdotool key --window chromium Tab
xdotool key --window chromium Return
echo "Finished"
```

- Altere os campos username e password para o usuário e senha de sua escolha.
- A linha sleep 60 é para que o raspberry consiga abrir o navegador por completo antes do xdotool começar a digitar o usuário e senha, este tempo pode ser aumentado. O valor 60 se refere ao número de segundos em que o script ficará aguardando até que sejam executados os outros comandos.
- Edite o arquivo de inicialização através do comando no terminal:

```sudo nano .config/lxsession/LXDE–pi/autostart```

- Adicione a linha a seguir no final do arquivo autostart:

```@bash /home/pi/logon.sh```
