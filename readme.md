# Configuração Raspberry

## Sistema Raspbian

### 1: Instalação do Raspbian
- Para realizar a instalação do raspbian é necessário acessar o site https://www.raspberrypi.org/downloads/ e realizar o download da ISO, conecte o Cartão MicroSD na máquina, realize a formatação com o **[SD Formatter](https://www.sdcard.org/downloads/formatter/)** e a instalação da ISO com o **[Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/)**.

### 2: Configuração Inicial
- Após a inicialização do raspberry, aparecerá a tela de configuração "Welcome to Raspberry Pi", são configurações simples e não existe a necessidade de conectar a um wifi e realizar a atualização neste momento, faremos logo em seguida.

### 3: Configuração de Endereço IP Estático
- Normalmente o raspbian vem com o DHCP ligado, se na sua rede os dispositivos ficam na configuração estática, é necessário mudar as configurações em **/etc/dhcpcd.conf**, alterar para configurações de exemplo abaixo.

Subir para privelégios root:
```
sudo su
```

Editar arquivo dhcpcd.conf:
```
nano /etc/dhcpcd.conf
```

No arquivo escreva/cole o texto abaixo alterando para as configurações de seu ambiente:
```
# Configuração de rede interface eth0
interface eth0
static ip_address=102.168.1.12/24
static routers=192.168.1.1
static domain_name_servers=8.8.8.8
```

### 4: Configurar acesso SSH
- Clique no menu iniciar do Raspbian, vá em **Preferences** e em **Raspberry Pi Configuration**;
- Em **Raspberry Pi Configuration**, selecione a aba **Interfaces**;
- Em **SSH**, selecione **enable** (utilizado para acesso ao sistema via SSH);
- Ao habilitar o acesso via SSH, o sistema irá recomendar a troca de senha do usuário, para isso só precisa acessar o terminal e digitar **passwd**;
- Na aba **Localisation**, podem ser configuradas as opções de localização, Timezone e linguagem de teclado;
- Para que todas estas configurações tenham efeito você deve reiniciar o sistema.

### 5: Configurar acesso VNC
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


### 6: Atualização de SO
- Rodar comandos abaixo:

```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get autoremove -y
```

### 7: Instalando requisitos do modo quiosque
- Rodar o comando abaixo:

```
sudo apt-get install chromium-browser ttf-mscorefonts-installer unclutter x11-xserver-utils -y
```

### 8: Configurar inicialização do SO
- Edite o arquivo autostart:

```
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
```

- Preencha da seguinte maneira:

```
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
@xscreensaver -no-splash
@point-rpi
@unclutter
@xset s off
@xset s noblank
@xset -dpms
@/usr/bin/chromium-browser --incognito --disable-gpu --start-maximized --kiosk https://google.com.br/
```

*o arquivo autostart pode estar em lugar diferente, na dúvida, rode o comando ```find . -name "autostart"``` acesse os arquivos via nano e veja qual apresenta o mesmo começo do código acima.*

### 9: Configuração para inserção automática de Login
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

### 10: Desativação de Wi-Fi onboard e Bluetooth
- Para desativar completamente o WiFi e Bluetooth integrado do firmware no Pi3 / Pi4, adicione em ```"/boot/config.txt"```:

```
dtoverlay=disable-wifi
dtoverlay=disable-bt
```

### 11: Instalação de NTP
- Para baixar o ntp execute:

```apt-get install ntp```

- Edite a configuração do ntp em ```"/etc/ntp.conf"``` para adicionar os servidores da nic nacionais com o horário local:

```
pool a.st1.ntp.br iburst
pool b.st1.ntp.br iburst
pool c.st1.ntp.br iburst
pool d.st1.ntp.br iburst
```

# Só usar agora, boa sorte.
