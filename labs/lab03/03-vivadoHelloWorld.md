# Hello world desde Zynq!

![alt text](../img/general/header.png)

### 1.1. Introducción

Este laboratorio te guiará en la creación del clásico Hello World para System-On-Chip basado en FPGA. 
<!-- This lab guides you through the process of creating a system with two GPIO IP Cores in the **PL** part of the **Zynq**. These **GPIOs** will be controlled by the ‘C’ application that will run in the **PS** part of the **Zynq**. One GPIO IP will be configured as an **output** port and will be connected to the **PMOD-A** Header of the Zedboard. Other GPIO IP will be configured as an **input** port and will be connected to the **PMOD-B** Header of the Zedboard. Both ports will be connected externally by a flat cable in a loopback mode. Some bits (more significant ones) of the input signal will be routed from the **PL** to the **PS** through the **EMIO**. Both, the writing data to the output port and the reading data from the input port will be done by a ‘C’ user-written application. The values will be displayed on the Host PC’s monitor through serial communication between the **PC-Host** and the **ZedBoard**. -->

### 1.2. Objetivos

- Adquirir conocimientos sobre el flujo de diseño SoC-FPGA utilizando la plataforma de software unificada Vitis.
- Crear el hardware para configurar la parte FPGA del SoC y configurar el PS.
- Crear la aplicación en 'C' (en Vitis) que se ejecutará en el PS.
- Probar el diseño completo en la plataforma ZedBoard (o PYNQ) para verificar el funcionamiento (Vitis y puerto serie).

<!-- ### 1.3. Descripcion del diseño

The following block diagram contains the main components of the design. 

 - PS: ARM CortexA9 and its configuration. 
 - ZedBoard elements: DDR memory, UART port, MIO Led, and two PMOD ports. -->

<!-- ![project block design](../uploads/Lab_1/design_bdj.png){width=80%} -->

## 2. Hardware

### 2.1. Proyecto de Vivado

1.   Abrir _Vivado 2022.2_.

2.   Desde el menu **Quick Start**, clic en ![create project](../img/general/create_project.png) para iniciar el asistente o haga clic en**File → Project → New**. Verá el cuadro de diálogo  **Create A New Vivado Project** en la ventana **New Project** . Haga clic en **Next**. Utilice la información de la siguiente tabla para configurar las diferentes opciones del asistente:

| Opcion | Propiedad del sistema | Configuracion | 
|---------------|-----------------|----------|
| Project Name | Project Name | Lab03 |  
  | Project Location | `/home/student/Documents/cursoML2025/labs` |
|  | Create Project Subdirectory | Check this option. | 
| clic **Next** |  |  |  
| Project Type | Project Type | Select **RTL Project**. Keep  unchecked the option `do not specify sources at this time`.  | 
| clic **Next** |  |  | 
| Add Sources | Do nothing |  |  
| clic **Next** |  |  |  
| Add Constraints | Do Nothing |  |  
| clic **Next** |  |  |  
| Default Part | Specify | Select **Boards** |  
|  | Board | Select **ZedBoard Zynq Evaluation and Development Kit** |  
| clic **Next** |  |  |  
| New Project Summary | Project Summary | Review the project summary |  
| clic **Finish** |  |  | 


Después de hacer clic en **Finish**, el **New Project Wizard** se cierra y el proyecto creado se abre en la interfaz principal de Vivado, que está dividida en dos secciones principales: **Flow navigator** y **Project Manager**. En el área de Project manager, se puede ver el **Project Summary**, donde se presentan la configuración, la parte de la placa seleccionada y los detalles de la síntesis. Para más detalles, haga clic [aquí](https://china.xilinx.com/support/documents/sw_manuals/xilinx2022_2/ug892-vivado-design-flows-overview.pdf). 

<!-- By selecting the **ZedBoard** platform, the **IP Integrator** is board aware and it will automatically assign dedicated PS IO ports to physical pin locations mapped to the specific board peripherals when the **Run Connection** wizard is used. Besides doing a pin constraint, **IP Integrator** also defines the I/O standard (LVCMOS 3.3, LVCMOS 2.5, etc) to each IO pin; saving time to the designer in doing so. -->

Al seleccionar la plataforma **ZedBoard**, el **IP Integrator** reconoce la placa y asignará automáticamente los puertos de E/S del PS a las ubicaciones de pines físicos correspondientes a los periféricos específicos de la placa cuando se utilice el asistente **Run Connection**. Además de establecer restricciones de pines (pin constraint), el **IP Integrator** también define el estándar de E/S (LVCMOS 3.3, LVCMOS 2.5, etc.) para cada pin de E/S, ahorrando tiempo al diseñador en este proceso.

### 2.2. Block Design
En esta sección, utilizaremos el **IP Integrator** para crear un proyecto de procesador embebido.

#### 2.2.1. Creación del Block design 

1. clic en **Create Block Design** en el panel **Flow Navigator**  dentro de **IP Integrator**.

![create block design](../img/lab03/create_block_design.png)

2. En la ventana emergente **Create Block Design**, definir el **Design Name** como *bd_helloWorld* y mantener las demás opciones por defecto. 

![block design configuration](../img/lab03/bd_config.png)

En la interfaz principal, en la sección **Block Design**, se presentará un nuevo lienzo en blanco de Diagrama, el cual se utilizará para crear el diseño de hardware que se implementará en el dispositivo Zynq.


#### 2.2.2. Instanciación y configuración del Sistema de Procesamiento (PS)

Para este laboratorio en particular, habilitaremos la interfaz **AXI_M_GP0**, los puertos **FCLK_RESET0_N** y **FCLK_CLK0**.

1. El primer paso es agregar el bloque Processing System (PS) de ZYNQ7. Puedes hacer esto haciendo clic en el ícono ![add ip icon](img/add_ip_icon.png) que aparece en la barra de herramientas en la sección de Diagrama, o en el área del lienzo en blanco, donde también puedes hacer clic derecho en el espacio en blanco del lienzo y seleccionar **Add IP** en las opciones disponibles.


![add PS](../img/lab03/add_ps.png)

Una pequeña ventana aparecerá mostrando los **IPs** disponibles (son cores de Propiedad Intelectual que ya están disponibles). Para buscar el IP core PS7, puedes desplazarte hasta la parte inferior de la lista de IPs o buscar el PS7 IP utilizando la palabra clave zynq. Haz doble clic en **ZYNQ7 Processing System** para seleccionarlo y agregarlo al lienzo.

Luego, el bloque **Zynq7 PS IP** se coloca en el lienzo del diagrama de bloques. Los puertos de E/S que se muestran en el bloque Zynq están definidos por la configuración predeterminada para este bloque.


![PS Placed at the block diagram](../img/lab03/ps_placed.png)

2. clic en **Run Block Automation**, disponible en la barra verde de información. 

![run automation](../img/lab03/run_automation.png)

3. Luego, en la ventana **Run Block Automation**, seleccionar **/processing_system7_0**. Asegurarse que **Apply Board Presets** se encuentra **seleccionado**, y dejar el resto de las opciones por defecto. clic **OK**.

![run automation window](../img/lab03/run_block_automation_window.png)

Después de completar el paso anterior, el diagrama de bloques debería verse como el siguiente:

![PS block automation result](../img/lab03/ps_block_automation.png)

4. Haz doble clic en el bloque **Zynq7 PS** para abrir la ventana **Re-customize IP** del Zynq 7 Processing System. **Todas las configuraciones necesarias para la unidad de procesamiento se completan en esta sección**.

	La ilustración del **Zynq block design** debería estar ahora abierta, detallando los diversos bloques configurables del Sistema de Procesamiento (recuerda que los bloques verdes son los configurables).

![Re-Customize IP window](../img/lab03/re_customize_ip.png)

5. Haz clic en **PS-PL Configuration** en el panel Page Navigator. Expande **General** y verifica que la tasa de baudios (baudrate) de UART1 esté configurada en **115200**.

![UART1 bautrate](../img/lab03/re_customize_uart1_bautrate.png)

6. Ve a **Peripherals I/O Pins** y verifica que los pines de E/S de Zynq estén asociados con UART1. 

![UART1 Pins](../img/lab03/re_customize_uart1_pin.png)

7. Haz clic en la opción **MIO Configuration** bajo el panel **Page Navigator**. Expande **I/O Peripherals**, desmarca todos los periféricos excepto el **UART1**. El **PS UART1** se utilizará para comunicar el dispositivo Zynq con el PC. *Esta comunicación se llevará a cabo utilizando un terminal serial como GTKTerm*.

![mio configuration UART1](../img/lab03/re_customize_mio_conf_uart1.png)

8. Expande **Application Processor Unit** y desmarca **Timer 0**. No lo estamos utilizando en este laboratorio.

![uncheck timer 0](../img/lab03/re_customize_mio_conf_timer0.png)

9. Haz clic en la opción  **Clock Configuration** y expande **PL Fabric Clocks**. Verifica que **FCLK_CLK0** esté habilitado y que su frecuencia esté configurada en **100 MHz**. **Esta sección define la frecuencia del reloj para el diseño digital del PL (Lógica Programable)**.

![Clock configuration](../img/lab03/re_customize_clock_conf.png)

10. Haz clic en la opción **DDR Configuration** y asegúrate de que la configuración **Enable DDR** esté seleccionada.

![DDR configuration](../img/lab03/re_customize_DDR_conf.png)

11. Finaliza la configuración del **Zynq** (processing_system7_0) haciendo clic en el botón ![OK](img/ok_btn.png) en la ventana **Re-Customize** IP.

> **Nota:** Para este laboratorio, no es necesario configurar el **SMC Timing Calculation** ni las **Interrupciones**.

De vuelta en el lienzo de diseño de bloques del proyecto, notarás que ahora se han añadido las interfaces **M_AXI_GPO**, **M_AXI_GPO_ACLK**, **FCLK_CLK0**, y los puertos **FCLK_RESET0_N** al bloque **Zynq7 PS**.


![PS configured](../img/lab03/ps_configured.png)

12. Conectar el puerto **FCLK_CLK0** al puerto **M_AXI_GP0_ACLK**.

El diseño final deberia ser como se muestra en la imagen siguiente: 

![Complete design](../img/lab03/hw.png)


### 2.3. Síntesis, implementación y generación de hardware

1. En el panel central, haz clic en la pestaña **Sources**. Expande **Design Sources** y verás que el archivo _bd_helloWorld.bd_ (board design) contiene todas las configuraciones y ajustes del diagrama de bloques creado en la ventana del editor de diagramas de bloques. Haz clic derecho sobre **bd_helloWorld** y selecciona **Generate Output Products**.

![generate output products](../img/lab03/generate_output_products.png)

<!-- 2. In the **Generate Output Products** window, at the **Synthesis Options** select **Global** *[r1]*. In the **Run Settings** *[r2]* section, then clic **Generate**. clic **OK** in the upcoming window.  --> **REVISAR** 

2. En la ventana **Generate Output Products**, en **Synthesis Options**, selecciona **Global** [r1]. En la sección **Run Settings** [r2], luego haz clic en **Generate**. Haz clic en **OK** en la ventana que aparecerá.

![generate output products window](../img/lab03/generate_output_products_window.png)

**Por favor, espera a que el proceso se complete antes de continuar.**


3. Haz clic derecho sobre **bd_helloWorld** y selecciona **Create HDL Wrapper** para crear el archivo de nivel superior en VHDL/Verilog a partir del diagrama de bloques.

![create hdl wrapper](../img/lab03/create_hdl_wrapper.png)

En la ventana **Create HDL Wrapper**, asegúrate de seleccionar la opción **Let Vivado manage wrapper and auto-update** para generar el archivo VHDL/Verilog. Esto permitirá que Vivado actualice automáticamente el HDL wrapper cuando realices cambios en el diseño del diagrama de bloques. Haz clic en **OK** para generar el wrapper.

Verás que se ha creado __bd_helloWorld_wrapper__ y se ha colocado en la parte superior de la jerarquía de archivos fuentes de diseño.

![top hdl wrapper](../img/lab03/top_hdl.png)


4. En el panel **Flow Navigator**, en la sección **SYNTHESIS**, haz clic en **Run Synthesis**. Este proceso convierte el diseño de alto nivel en una descripción de hardware que puede ser implementada en el dispositivo FPGA.

<!-- ![Alt text](../img/lab03/launch_runs_synthesis.png) -->

10. En el panel **Flow Navigator**, haz clic en **Run Implementation**. En la ventana emergente, haz clic en **OK**. La implementación es el paso en el que Vivado mapea el diseño sintetizado en los recursos físicos del FPGA. Este proceso puede demorar un poco dependiendo de la complejidad del diseño.


11. En el menú **Flow Navigator**, haz clic en Generate Bitstream. Déjalo tal como está y haz clic en **OK**. Después de finalizar la generación del bitstream, deja la opción *View Report* marcada. La generación de hardware crea los archivos necesarios para cargar el diseño en el dispositivo Zynq.

12. Dado que es necesario generar una aplicación para usar el diseño, se debe exportar el hardware al entorno de Vitis. Haz clic en: **File -> Export -> Export Hardware**. Dado que hay algo de lógica en la parte PL del Zynq, el bitstream correspondiente debe incluirse en la tarea de exportación. Por lo tanto, asegúrate de marcar la casilla **Include bitstream**. Luego, haz clic en **Next**.

![export hardware](../img/lab03/export_hardware.png)

13. La plataforma de hardware se exportará como un archivo **XSA**. Para completar el proceso, haz clic en **Finish**.

![Alt text](../img/lab03/xsa_save_file.png)

En este punto, tu diseño y configuración de hardware están listos, y puedes proceder a generar un proyecto de aplicación en Vitis, donde se utilizará el **lenguaje de programación C** en el microprocesador ARM.

## 3. Software

### 3.1. Ejecutar Vitis IDE y configurar el espacio de trabajo



1. En Vivado, ejecuta Vitis IDE haciendo clic en  **Tools > Launch Vitis IDE**. Se abrirá el cuadro de diálogo Lanzar Vitis IDE, solicitando el directorio del espacio de trabajo. Haz clic en **Browse**  para especificar el directorio del espacio de trabajo. **Como recomendación, usa el mismo directorio de tu proyecto.**


![Vitis workspace](../img/lab03/vitis_workspace.png)

## 3.2. Vitis Application Project

En esta sección, crearás el proyecto de aplicación en ‘C’ y el código para utilizar el diseño de hardware.

1. Seleccionar **File -> New -> Application Project** o en la ventana principal clic en ![application project](../img/general/create_application_project.png). Para crear la nueva aplicación, utiliza la siguiente información:


| Pantalla del Asistente | Propiedad del Sistema | Configuración |
|---------------|-----------------|----------|
| Create a New Application Project | | |
| clic **Next** | | |
| Platform | clic **Create a new platform from hardware** | |
| | XSA Flie: | Browse: **bd_helloWorld_wrapper.xsa** \* |
| clic **Next** | | |
| Application Project Details | Application project name | app_helloWorld |
| clic **Next** | | |
| Domain | Name: | standalone_ps7_cortexa9_0 |
| | Display Name: | standalone_ps7_cortexa9_0 |
| | Operating System: | standalone | 
| | Processor: | ps7_cortexa9_0 |
| | Architecture: | 32-bit |
| clic **Next** | | |  
| Templates | Available Templates: | Hello World | 
| clic **Finish** | | | 

> **\*** 🔔 Recuerda que el archivo **XSA** fue exportado a la carpeta de tu proyecto **/home/student/Documents/cursoML2025/labs/Lab03**.

Después de hacer clic en **Finish**, **New Application Project Wizard** se cierra y el proyecto creado se abre en la interfaz principal de Vitis, que está dividida en las secciones: Explorer, Assistant, Project Editor y Console. En las vistas Explorer y Assistant, podrás ver dos elementos creados: ![app icon](../img/general/app_icon.png) application y and ![platform icon](../img/general/platform_icon.png) platform project.

![Vitis main gui](../img/lab03/vitis_main_gui.png)

## 4. Testing

### 4.1.  Configuración de la placa de desarrollo Zedbord

En esta sección, aprenderás los pasos para conectar la ZedBoard al PC y ejecutar la aplicación en lenguaje 'C' que acabas de escribir.

1. Conecta un cable micro USB entre la máquina host con Linux y el **JTAG J17** de la placa de desarrollo ZedBoard (en el lado derecho del conector de alimentación). Esta conexión se usará para configurar la PL.

2. Conecta un cable micro USB al conector **USB UART (J14)** de la placa de desarrollo ZedBoard (en el lado izquierdo del interruptor de encendido), con la máquina host con Linux. Esta conexión se usará para transferir datos en serie entre la ZedBoard y la máquina host.

![microusb connectors](../img/lab03/microusb_connectors_v2.png)

> **🔥 IMPORTANTE**: Asegúrate de que los jumpers **JP7** a **JP11** estén configurados como se muestra en la imagen siguiente para el modo de configuración **JTAG**.

![jtag config](../img/lab03/jtag_conf_v2.png)

3. Verifica la configuración del jumper **J18** en la esquina inferior derecha de la placa. El jumper debe estar configurado a **2.5V**, marcado como “**2V5**” en la placa.

![power jumper](../img/lab03/power_jumper_v2.png)

4. Conecta el cable de alimentación del convertidor AC/DC de 12V al conector de alimentación de la ZedBoard.

5. **Enciende** la placa usando el **interruptor de encendido** de la ZedBoard. Verifica que el **LED de encendido** en la placa (LED verde) esté encendido.


![power sw](../img/lab03/power_sw_v2.png)

### 4.2. Configuración del software de comunicación serial

1. Se hará uso de **GTKTerm** para establecer la comunicación serial entre la máquina host y la ZedBoard. Para configurar GTKTerm, abre el software haciendo clic en **Applications -> Accessories -> Serial port terminal**.

![gtkterm](../img/lab03/gtkterm_open.png){width=50%}

Aparecerá una ventana similar a la que se muetra a continuación:

![gtkterm](../img/lab03/gtkterm.png)

2. Haz clic en **Configuration > port** y selecciona el puerto **ttyACM0** con Baud Rate **115200**.


![Port Config](../img/lab03/gtkterm_config_port.png){width=50%}

### 4.3.  Ejecutar la aplicación en la ZedBoard

1. En el software Vitis, haz clic derecho sobre *app_helloWorld*, luego selecciona **Run -> Run As -> Launch on Hardware (Single application debug (GBD))** para reconfigurar la FPGA y ejecutar el código compilado en el PS.

2. En Vitis, aparecerá una ventana emergente indicando que la FPGA está siendo programada. Al finalizar este proceso, el LED azul en la ZedBoard se encenderá.

![Program FPGA](../img/lab03/fpga_program.png)

3. Vuelve a la ventana de **GTKTerm**. Si todo funciona correctamente, deberías ver el resultado de la ejecución de la aplicación en la consola serial remota.

