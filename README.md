Visão geral
======

Este repositório contém o projeto da placa de circuito impresso (PCB) e os drivers para o projeto de uma placa carrier Raspberry Pi CM4 com tela MIPI de alta resolução.<br>
Para mais detalhes, acesse https://hackaday.io/project/176098-raspberry-pi-cm4-arrier-with-hi-res-mipi-display

A tela utilizada é a JDI LT070ME05000, com circuito integrado controlador de toque da Goodix. O projeto também usa um expansor de E/S MCP23008 para economizar alguns pinos GPIO.

Por padrão, o Raspberry Pi oferece suporte apenas à sua tela oficial MIPI DSI de 7 polegadas com FKMS (Fake/Firmware KMS).
Para que esta tela funcione, é necessário usar o KMS completo e um driver de painel para o kernel Linux.

O projeto da PCB está no diretório KiCAD.

Configuração
======

1. Instale a versão mais recente do Raspberry Pi OS em um cartão SD (com o CM4 Lite) ou na eMMC.
   Para a versão Lite, siga as [instruções para o cartão SD](https://www.raspberrypi.org/documentation/installation/installing-images).
   Caso contrário, siga as [instruções para a eMMC](https://www.raspberrypi.org/documentation/hardware/computemodule/cm-emmc-flashing.md).
   O jumper que habilita a inicialização por USB fica próximo ao conector micro-USB na PCB.
   
1. Monte a partição `boot` no computador.

1. Habilite e conecte o console serial.
   1. Adicione `enable_uart=1` ao arquivo config.txt na partição `boot`.
   1. Use a interface serial para se conectar ao Raspberry Pi: [instruções do elinux.org](https://elinux.org/RPi_Serial_Connection).
   
1. Habilite a porta USB.<br>
   Por padrão, a USB fica desabilitada no CM4 para economizar energia.<br>
   Para habilitá-la, adicione `dtoverlay=dwc2,dr_mode=host` ao config.txt.
   
1. Inicialize o Raspberry Pi e conecte-o à internet. Se o módulo não tiver Wi-Fi,
   será necessário usar um adaptador Wi-Fi USB ou Ethernet.
   Se o Wi-Fi não funcionar e, ao tentar configurar a rede sem fio, o raspi-config exibir uma mensagem como "Could not communicate with wpa_supplicant",
   talvez seja necessário adicionar as linhas a seguir
   ```
   allow-hotplug wlan0
   iface wlan0 inet manual
   wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
   ```
   ao arquivo /etc/network/interfaces e reinicializar o sistema.

1. No Raspberry Pi:
   1. Instale o git e o dkms:
   ```
   sudo apt-get update
   sudo apt-get install git dkms
   ```
   2. Clone o repositório e instale os drivers:
   ```
   git clone https://github.com/renetec-io/cm4-panel-jdi-lt070me05000.git
   cd cm4-panel-jdi-lt070me05000
   ./setup.sh
   ```

1. Habilite o driver da tela.<br>
   Adicione as linhas a seguir ao final de /boot/config.txt:
   ```
   dtparam=i2c_vc=on   
   lcd_ignore=1   
   dtoverlay=vc4-kms-v3d-pi4,noaudio   
   dtoverlay=cm4-dsi-lt070me05000   
   ```
   
1. Habilite o áudio.<br>
   Adicione as linhas a seguir a /boot/config.txt:
   ```
   # Configuração de áudio
   dtparam=audio=on   
   dtoverlay=hifiberry-dac   
   dtoverlay=i2s-mmap   
   ```   

8. Reinicialize o sistema.
