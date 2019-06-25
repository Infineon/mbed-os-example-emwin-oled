-------------------------------------------------------------------------------
## mbed-os-example-emwin-oled

### Requirements

[Mbed CLI](https://github.com/ARMmbed/mbed-cli) 

Programming Language: C++

Associated Parts: All [PSoC® 6 MCU](http://www.cypress.com/PSoC6) parts

### Supported Kits

[PSoC 6 BLE Pioneer Kit](https://www.cypress.com/CY8CKIT-062-BLE) + [CY8CKIT-032 AFE Shield](https://www.cypress.com/documentation/development-kitsboards/cy8ckit-032-psoc-analog-front-end-afe-arduino-shield)

[PSoC 6 WiFi-BT Pioneer Kit](https://www.cypress.com/cy8ckit-062-wifi-bt) + [CY8CKIT-032 AFE Shield](https://www.cypress.com/documentation/development-kitsboards/cy8ckit-032-psoc-analog-front-end-afe-arduino-shield)

### Overview

This code example demonstrates how to display graphics on an OLED display using emWin graphics library in Mbed OS.  The example uses the [CY8CKIT-032 AFE Shield](https://www.cypress.com/documentation/development-kitsboards/cy8ckit-032-psoc-analog-front-end-afe-arduino-shield). This shield has a monochrome OLED display with SSD1306 controller and a resolution of 128x64 pixels.

### Hardware Setup

This example uses the [CY8CKIT-032 AFE Shield](https://www.cypress.com/documentation/development-kitsboards/cy8ckit-032-psoc-analog-front-end-afe-arduino-shield) paired with either the [PSoC 6 BLE Pioneer Kit](https://www.cypress.com/CY8CKIT-062-BLE) or [PSoC 6 WiFi-BT Pioneer Kit](https://www.cypress.com/cy8ckit-062-wifi-bt).

This example uses the kit’s default configuration. Refer to the kit guide to ensure the kit is configured correctly.

**Notes**: 

1. The PSoC 6 BLE Pioneer kit and the PSoC 6 WiFi-BT Pioneer kit ship with KitProg2 installed. Med OS only works with KitProg3. Before using this code example, make sure that the kit is upgraded to KitProg3.  Instructions to upgrade to KitProg3 may be found in the below [Firmware Loader](https://github.com/cypresssemiconductorco/Firmware-loader) GitHub repository.
2. Once the firmware is upgraded, the kit has to be put in DAPLINK mode for Mbed to be able to program the board.  Refer the **KitProg3 User Guide** (found in the Documentation tab in the [Cypress Programming Solutions](https://www.cypress.com/products/psoc-programming-solutions) web page) for details of how to put the Pioneer Kit into DAPLINK mode.

### Software Setup

This example requires that [Mbed CLI](https://github.com/ARMmbed/mbed-cli)  is installed in the computer.  No other software tools are required.

### Importing, Compiling and Running the Code Example

Import the code example into your mbed directory using following mbed command.

```
mbed import https://github.com/cypresssemiconductorco/mbed-os-example-emwin-oled
```

Change working directory to the code example folder

```
cd mbed-os-example-emwin-oled
```

Make sure the board is plugged in. Then compile the code and program the target board.

```
mbed compile --toolchain GCC_ARM --target CY8CKIT_062_BLE --flash
```

or

```
mbed compile --toolchain GCC_ARM --target CY8CKIT_062_WIFI_BT --flash
```

### Operation

Once the project is compiled and the board is progra	mmed, reset the board.

- Startup screen with Cypress logo is displayed on the OLED display for 3 seconds.
- After 3 seconds, another screen appears with instructions to press SW2 to scroll through the display pages.
- Press SW2 to scroll through various display pages that demonstrate the emWin graphic library's 2D graphics features:
  - Text wrap and rotation
  - Font sizes - normal fonts
  - Font sizes - bold fonts
  - Text alignments and styles
  - 2D Graphics with vertical lines of various thickness, rectangles and circles

### Design and Implementation

Let us take a look at how this project was created and configured.  There are three important steps in creating an Mbed OS project for emWin.  

For instructions to add emWin in an Mbed project refer the following web page.

[Cypress emWin Middleware Overview](https://cypresssemiconductorco.github.io/middleware-emwin/emwin_overview/html/index.html)

**Note:** This section shows the step by step procedure of how this project was created from scratch.  To run the code example out of box, you just need to follow the steps explained in the  "Importing, Compiling and Running the Code Example" section.

#### Step #1 Creating a new project and adding emWin middleware to the project

1. Create a new Mbed project by using the following command.  
    
    `mbed new mbed-os-example-emwin-oled`

    This creates a new folder with the project name and imports Mbed OS into the folder.

2. Change to the newly created project folder.
   
    `cd mbed-os-example-emwin-oled`

3. Add emWin middleware to the project by using following command.
    
    `mbed add https://github.com/cypresssemiconductorco/middleware-emwin`

    This command imports emWin library files into the Mbed project.

#### Step #2 Configuring emWin

1. Decide the core option and update the mbed_apps.json file by adding the following code to the targets override section.
   
    ```
    "target_overrides": {
        "*":{
        "target.components_add": ["EMWIN_NOSNTS"],
    }
    
    ```
    
    In this project, the NOSNTS option has been selected which means no OS support and no Touch Screen support.  Though Mbed OS is essentially an OS that supports multi threading, this option has been selected as emWin is called only from one thread, that is the main.cpp.  If your project calls emWin from multiple threads, then use the EMWIN_OSNTS or EMWIN_OSTS options.
    
2. Decide the emWin display driver to use for the display controller.  Details of display drivers and supported display controllers can be found in "Chapter 33 Display Drivers" in the emWin User Guide.  The OLED display has the SSD1306 controller.  This controller is supported by GUIDRV_SPage driver. 

3. Create a folder emwin_config and copy following files.

    - Copy LCDConf.c file from the folder middleware-emwin\Sample\LCDConf\GUIDRV_SPage\Generic.  Rename it to LCDConf.cpp

    - Copy LCDConf.h file from the folder middleware-emwin\Sample\LCDConf\GUIDRV_SPage

    - Copy GUI_X_Mbed.cpp from the folder middleware-emwin\Sample\GUI_X folder

    - Copy GUIConf.c from the folder middleware-emwin\Sample\GUIConf.  Rename this file to GUIConf.cpp

4. Write port access functions for I2C.  For this project, i2c_portapi.cpp and i2c_portapi.h that have been created.  emWin requires functions to write single command byte, write single data byte, write multiple data bytes and read multiple data bytes.

    We need to configure the I2C port in Mbed.  For this, the following code is added in mbed_apps.json file.

    ```json
    "config": {
            "DISPLAY_SDA":   "DISPLAY_SDA",
            "DISPLAY_SCL":   "DISPLAY_SCL"
    },
    ```
    The following code is added to the target_overrides section

    ```json
    "target_overrides": {
        "*":{
            "DISPLAY_SDA":   "P6_1",
            "DISPLAY_SCL":   "P6_0",
        }
    }
    ```
    When the project is compiled, Mbed OS creates macros in the mbed_config.h file based on information from the .json files.  These macros are used in the i2c_portapi.cpp file to setup the I2C interface.

    ```c++
    /* I2C port to communicate with the OLED display controller */
    I2C Display_I2C(MBED_CONF_APP_DISPLAY_SDA, MBED_CONF_APP_DISPLAY_SCL);
    ```

5. Modify LCDConf.cpp for the display controller being used in the project.  More details on these configurations may be found in the emWin User Guide.

    Set the display size for X and Y

    ```C++
    #define XSIZE_PHYS 128
    #define YSIZE_PHYS 64
    ```

    Set the color conversion.  This display uses monochrome which is GUICC_1

    ```C++
    #define COLOR_CONVERSION GUICC_1
    ```

    Set the display driver

    ```C++
    #define DISPLAY_DRIVER GUIDRV_SPAGE_1C1
    ```

    In the LCD_X_Config function, update emWin port access pointers with the I2C port access functions in 		i2c_portapi.cpp.

    ```C++
    PortAPI.pfWrite8_A0  = I2C_Write00;
    PortAPI.pfWrite8_A1  = I2C_Write01;
    PortAPI.pfWriteM8_A1 = I2C_WriteM01;
    ```

    In the LCD_X_Config function, setup the driver.

    ```C++
    GUIDRV_SPage_Set1510(pDevice);
    ```

6. Write display driver initialization code in the _InitController function. The LCDConf.cpp file in this code example has a section with macro defines for all register addresses of the SSD1306 controller, and the _InitController function is updated with code that writes to various registers of SSD1306 to configure the display.  These details are available in the SSD1306 data sheet.

There are some other advanced settings (like memory allocation) that can be configured as well. These are not covered in this documentation. Details can be found in emWin User Guide.

#### Step #3 Writing application code

Application code for emWin is straightforward.  Call the GUI_Init function at the beginning of main, and call emWin graphics functions (GUI_SetFont, GUI_DrawLine, GUI_DrawRoundedRect etc) to update the display with text and graphics.  Function names and usage details can be found in emWin User Guide.

### Reusing This Example

This example can be run on CY8CKIT-062-BLE and CY8CKIT-062-WiFi-BT.  No code changes are required in the project to support these kits.  The only requirement is to select the correct target in the "mbed compile" command.

For CY8CKIT-062-BLE:

​	`mbed compile --toolchain GCC_ARM --target CY8CKIT_062_BLE --flash`

For CY8CKIT-062-WiFi-BT:

​	`mbed compile --toolchain GCC_ARM --target CY8CKIT_062_WIFI_BT --flash`

### Related Resources

|                                                              |                                                              |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| **Device Documentation**                                     |                                                              |
| [PSoC 6 MCU Datasheets](http://www.cypress.com/search/all?f[0]=meta_type%3Atechnical_documents&f[1]=resource_meta_type%3A575&f[2]=field_related_products%3A114026) | [PSoC 6 Technical Reference Manuals](http://www.cypress.com/search/all/PSoC 6 Technical Reference Manual?f[0]=meta_type%3Atechnical_documents&f[1]=resource_meta_type%3A583) |
| **Development Kits**                                         |                                                              |
| [CY8CKIT-062-BLE PSoC 6 BLE Pioneer Kit](http://www.cypress.com/CY8CKIT-062-BLE) | [CY8CKIT-062-WiFi-BT PSoC 6 WiFi-BT Pioneer Kit](http://www.cypress.com/CY8CKIT-062-WiFi-BT) |
| [CY8CKIT-032 AFE Shield](https://www.cypress.com/documentation/development-kitsboards/cy8ckit-032-psoc-analog-front-end-afe-arduino-shield) |                                                              |
| **Middleware**                                               | Middleware libraries are distributed on GitHub               |
| [emWin Middleware](https://github.com/cypresssemiconductorco/middleware-emwin) |                                                              |
| **Tools**                                                    |                                                              |
| [Cypress Programmer](https://www.cypress.com/products/psoc-programming-solutions) | KitProg3 User Guide - Go to Documentation tab in [Cypress Programming Solutions](https://www.cypress.com/products/psoc-programming-solutions) |
|                                                              |                                                              |
#### Other Resources

Cypress provides a wealth of data at www.cypress.com to help you to select the right device, and quickly and effectively integrate the device into your design.

For the PSoC 6 MCU devices, see [KBA223067](https://community.cypress.com/docs/DOC-14644) in the Cypress community for a comprehensive list of PSoC 6 MCU resources.

### Document History

Document Title: emWin OLED for Mbed OS

| Revision | Orig. of Change | Submission Date | Description of Change |
| -------- | --------------- | --------------- | --------------------- |
| **       | GRAA            | 6/25/2019       | New code example      |

------

All other trademarks or registered trademarks referenced herein are the property of their respective
owners.

![Banner](images/Banner.png)

-------------------------------------------------------------------------------

© Cypress Semiconductor Corporation, 2019. This document is the property of Cypress Semiconductor Corporation and its subsidiaries (“Cypress”).  This document, including any software or firmware included or referenced in this document (“Software”), is owned by Cypress under the intellectual property laws and treaties of the United States and other countries worldwide.  Cypress reserves all rights under such laws and treaties and does not, except as specifically stated in this paragraph, grant any license under its patents, copyrights, trademarks, or other intellectual property rights.  If the Software is not accompanied by a license agreement and you do not otherwise have a written agreement with Cypress governing the use of the Software, then Cypress hereby grants you a personal, non-exclusive, nontransferable license (without the right to sublicense) (1) under its copyright rights in the Software (a) for Software provided in source code form, to modify and reproduce the Software solely for use with Cypress hardware products, only internally within your organization, and (b) to distribute the Software in binary code form externally to end users (either directly or indirectly through resellers and distributors), solely for use on Cypress hardware product units, and (2) under those claims of Cypress’s patents that are infringed by the Software (as provided by Cypress, unmodified) to make, use, distribute, and import the Software solely for use with Cypress hardware products.  Any other use, reproduction, modification, translation, or compilation of the Software is prohibited.
TO THE EXTENT PERMITTED BY APPLICABLE LAW, CYPRESS MAKES NO WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, WITH REGARD TO THIS DOCUMENT OR ANY SOFTWARE OR ACCOMPANYING HARDWARE, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  No computing device can be absolutely secure.  Therefore, despite security measures implemented in Cypress hardware or software products, Cypress shall have no liability arising out of any security breach, such as unauthorized access to or use of a Cypress product.  CYPRESS DOES NOT REPRESENT, WARRANT, OR GUARANTEE THAT CYPRESS PRODUCTS, OR SYSTEMS CREATED USING CYPRESS PRODUCTS, WILL BE FREE FROM CORRUPTION, ATTACK, VIRUSES, INTERFERENCE, HACKING, DATA LOSS OR THEFT, OR OTHER SECURITY INTRUSION (collectively, “Security Breach”).  Cypress disclaims any liability relating to any Security Breach, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any Security Breach.  In addition, the products described in these materials may contain design defects or errors known as errata which may cause the product to deviate from published specifications.  To the extent permitted by applicable law, Cypress reserves the right to make changes to this document without further notice. Cypress does not assume any liability arising out of the application or use of any product or circuit described in this document.  Any information provided in this document, including any sample design information or programming code, is provided only for reference purposes.  It is the responsibility of the user of this document to properly design, program, and test the functionality and safety of any application made of this information and any resulting product.  “High-Risk Device” means any device or system whose failure could cause personal injury, death, or property damage.  Examples of High-Risk Devices are weapons, nuclear installations, surgical implants, and other medical devices.  “Critical Component” means any component of a High-Risk Device whose failure to perform can be reasonably expected to cause, directly or indirectly, the failure of the High-Risk Device, or to affect its safety or effectiveness.  Cypress is not liable, in whole or in part, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any use of a Cypress product as a Critical Component in a High-Risk Device.  You shall indemnify and hold Cypress, its directors, officers, employees, agents, affiliates, distributors, and assigns harmless from and against all claims, costs, damages, and expenses, arising out of any claim, including claims for product liability, personal injury or death, or property damage arising from any use of a Cypress product as a Critical Component in a High-Risk Device.  Cypress products are not intended or authorized for use as a Critical Component in any High-Risk Device except to the limited extent that (i) Cypress’s published data sheet for the product explicitly states Cypress has qualified the product for use in a specific High-Risk Device, or (ii) Cypress has given you advance written authorization to use the product as a Critical Component in the specific High-Risk Device and you have signed a separate indemnification agreement.
Cypress, the Cypress logo, Spansion, the Spansion logo, and combinations thereof, WICED, PSoC, CapSense, EZ-USB, F-RAM, and Traveo are trademarks or registered trademarks of Cypress in the United States and other countries.  For a more complete list of Cypress trademarks, visit cypress.com.  Other names and brands may be claimed as property of their respective owners.