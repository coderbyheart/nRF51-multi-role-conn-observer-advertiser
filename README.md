#Advertiser With and Without Timeslot API

This project is an example of using timeslot API to built an scannable and connectable advertiser . The advertiser can run with two modes : with and without timeslot API but it is not selectable in the run time .
The mode in which one would wish to run this advertiser should be set before compilation by using preprocessor directive at the start of the main.c file , ts_controller.c file and nrf_advertiser.c file of the advertiser project .

This project utilizes the Concurrent Multi-protocol Timeslot API in Nordic Semiconductor's S110 v8.0 SoftDevice for the nRF51-series micro-controllers to run a scannable and connectable advertiser concurrently with the SoftDevice. The provided build scripts and project files support both the PCA10028 and PCA10031 development boards.


The advertiser handles the following packets to advertise ,scan and connect :

(a) Advertising channel packets : ADV_IND , ADV_DIRECT_IND, ADV_NONCONN_IND, ADV_SCAN_IND, SCAN_REQ,SCAN_RSP
(b) Data Channel packets : Data (unencrypted)
(c) Data Channel Control packets : LL_CONNECTION_UPDATE_REQ , LL_CHANNEL_MAP_REQ,LL_TERMINATE_IND ,LL_FEATURE_REQ ,  LL_FEATURE_RSP,LL_VERSION_IND


An algorithom has been implemented to find data channel index from the channel map provided by the CONN_REQ PDU and LL_CHANNEL_MAP_REQ PDU .The timeslot and non timeslot version of the advertiser only uses the TIMER0 for all of its application.

The timeslot version of the advertiser has a feature to handle the clock drift .This clock drift should be handled in timelsot version because the packets should be received and transmitted within the allowed timeslot span . Otherwise the packets would be missed frequently which would result in connection loss . The timeslot is requested in every ( connection interval + drift time ) interval instead of only connection interval to compensate the time for crystal drift of the peer device .The drift time has been calculated by using TIMER0 inside the advertiser .

Required Software
•S110 v8.0 production SoftDevice
•nRF51 SDK v9.0
•Keil uVision 5 or an arm-none-eabi GCC toolchain

Clone this GitHub repository into the examples/ folder of the nRF51 SDK directory.The copying path is important and a tutorial related to this can be found in the following link  https://devzone.nordicsemi.com/tutorials/19/.

Connection established with : Master control Panel version 3.10.0.14 on PC  , nrf master control panel version 4.0.5 on Android (version 5.1.1) phone and Light Blue on iPhone (IOS 9.2.1) . 

Example code

The project contains an example of advertiser usage, where the Timeslot advertiser runs with an advertisement interval of 120ms, alongside a SoftDevice-powered connectable advertiser with an advertisement interval of 150ms .

Timeslot Advertiser: Interface

The timeslot advertiser interface is based on the Bluetooth standard HCI interface, but only implements a small subset of the functionality. All interface functions are found in the Include/nrf_advertiser.h file

Available function calls:

void btle_hci_adv_init(IRQn_Type btle_hci_adv_evt_irq);

Initialize the timeslot advertiser. The IRQ parameter decides which software interrupt to use for the generated events. For each report the advertiser generates, the selected IRQ flag will be set, and the user may get events from the queue using btle_hci_adv_evt_get().


bool btle_hci_adv_report_get(nrf_report_t* report);

Function for getting pending tsa reports. It is recommended to repeat this function until it returns false, as there may be more than one pending report in the queue.


void btle_hci_adv_sd_evt_handler(uint32_t event);

SoftDevice event handler for the timeslot advertiser. Takes any timeslot event generated by SoftDevice function sd_evt_get(). 


void btle_hci_adv_params_set(btle_cmd_param_le_write_advertising_parameters_t* adv_params);

Set the advertisement parameters for the timeslot advertiser. See hci/btle.h for details about the struct parameter


void btle_hci_adv_enable(btle_adv_mode_t adv_enable);

Enable or disable advertisement


void btle_hci_adv_data_set(btle_cmd_param_le_write_advertising_data_t* adv_data);

Set the content of the timeslot advertiser advertisement packets. This does not include any headers or address, as this is controlled internally. To set the advertisement address, use the btle_hci_adv_params_set() function.


void btle_hci_adv_scan_rsp_data_set(btle_cmd_param_le_write_scan_response_data_t* scan_rsp);

Set the content of the timeslot advertiser scan response packet. This is the packet the beacon will send to respond to external scan requests. This does not include any headers or address, as this is controlled internally. To set the advertisement address, use the btle_hci_adv_params_set() function.


void btle_hci_adv_whitelist_add(btle_cmd_param_le_add_device_to_whitelist_t* whitelist_device);

Add a scanner to the device whitelist. If the whitelist filter is enabled, only scanners on this whitelist will be accepted. 

The filter can be enabled with the btle_hci_adv_params_set() function above.


void btle_hci_adv_whitelist_remove(btle_cmd_param_le_remove_device_from_whitelist_t* whitelist_device);

Remove a scanner from the device whitelist. If the whitelist filter is enabled, only scanners on this whitelist will be accepted. 

The filter can be enabled with the btle_hci_adv_params_set() function above.


void btle_hci_adv_whitelist_flush(void);

Remove all scanners from the device whitelist. If the whitelist filter is enabled, only scanners on this whitelist will be accepted. 

The filter can be enabled or disabled with the btle_hci_adv_params_set() function above. Note that this function does not disable the filter functionality





