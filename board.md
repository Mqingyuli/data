```c
/*******************************************************************************


 *******************************************************************************/
#ifndef CUSTOM_BOARD_H
#define CUSTOM_BOARD_H

#include "nrf_gpio.h"

#ifdef __cplusplus
extern "C"
{
#endif

/* Device basic info */
#define DEV_MODEL "Locator"
#define MANUFACTURER_NAME "SpiderSens"

#define LOCATECARD_V4_3  0
#define LOCATECARD_V4_5  0
#define LOCATECARD_V5_3  1



  extern char SWInforamtion[64];
  extern char DeviceName[64];

/* Functions Control */
#define BLE_EN 0               //蓝牙功能
#define DFU_EN 1               // DFU功能
#define SAVE_POWER_POLICY_EN 1 //省电模式
#define LORA_EN 1             // Lora使能
#define LTE_EN 0              // LTE使能
#define GNSS_EN 0              // GNSS使能
#define BAT_STDBY_PIN ME4057_STDBY_PIN
#define BAT_CHRG_PIN ME4057_CHRG_PIN
#define LED_GREEN_PIN LED1_GREEN_PIN
#define LED_BLUE_PIN LED2_BLUE_PIN
#define LED_RED_PIN LED3_RED_PIN

#if (LOCATECARD_V4_3)
/* Version inforamtion */
#define HW_VER "V4.3"
#define SW_VER "V3.x.x"

/* UART Id */
#define UART_AIR724 1
#define UART_GPS 2

#define NRF_GPIO_PIN_RESET NRF_GPIO_PIN_MAP(0, 18)
/* To LTE cat1 Air724 */
#define LTE_UART_TX_PIN NRF_GPIO_PIN_MAP(0, 8)
#define LTE_UART_RX_PIN NRF_GPIO_PIN_MAP(1, 9)
#define LTE_PWRKEY_PIN NRF_GPIO_PIN_MAP(0, 6)
#define LTE_STATUS_PIN NRF_GPIO_PIN_MAP(0, 16)

/* To GPS */
#define GNSS_PWRCTRL_PIN NRF_GPIO_PIN_MAP(0, 15)
#define GNSS_UART_TX_PIN NRF_GPIO_PIN_MAP(0, 22)
#define GNSS_UART_RX_PIN NRF_GPIO_PIN_MAP(0, 20)
#define GNSS_FORCE_ON_PIN NRF_GPIO_PIN_MAP(0, 24)

/* To Lora SX1268 */
#define SX1268_PWRCTRL_PIN NRF_GPIO_PIN_MAP(0, 3)
#define SX1268_RESET_PIN NRF_GPIO_PIN_MAP(0, 31)
#define SX1268_TCXOEN_PIN NRF_GPIO_PIN_MAP(0, 7)
#define SX1268_BUSY_PIN NRF_GPIO_PIN_MAP(1, 11)

#define SX1268_DIO1_PIN NRF_GPIO_PIN_MAP(1, 12)
#define SX1268_DIO2_PIN NRF_GPIO_PIN_MAP(1, 14)
#define SX1268_DIO3_PIN NRF_GPIO_PIN_MAP(0, 28)
#define SX1268_ANT_SW NRF_GPIO_PIN_MAP(1, 10)

#define SPI2_SCK_PIN NRF_GPIO_PIN_MAP(1, 15)
#define SPI2_MISO_PIN NRF_GPIO_PIN_MAP(0, 29)
#define SPI2_MOSI_PIN NRF_GPIO_PIN_MAP(0, 2)
#define SX1268_CS_PIN NRF_GPIO_PIN_MAP(1, 13)

/* I2C:  ICM20600 & CW2015 share one I2C, BMP280 use another */
#define TWI0_SCL_PIN NRF_GPIO_PIN_MAP(0, 14)
#define TWI0_SDA_PIN NRF_GPIO_PIN_MAP(0, 12)
#define TWI1_SCL_PIN NRF_GPIO_PIN_MAP(1, 8)
#define TWI1_SDA_PIN NRF_GPIO_PIN_MAP(0, 11)

/* LED, Button, Beep */
#define LED1_GREEN_PIN NRF_GPIO_PIN_MAP(0, 27) // swap LED1 and LED2 pin as color. now LED1 blue,
#define LED2_BLUE_PIN NRF_GPIO_PIN_MAP(0, 30)
#define LED3_RED_PIN NRF_GPIO_PIN_MAP(0, 5)

#define BUTTON_PIN NRF_GPIO_PIN_MAP(0, 4)
#define BEEP_PIN NRF_GPIO_PIN_MAP(0, 25)

/* Charge */
#define ME4057_STDBY_PIN NRF_GPIO_PIN_MAP(0, 13)
#define ME4057_CHRG_PIN NRF_GPIO_PIN_MAP(0, 17)

#define ICM20600_INT NRF_GPIO_PIN_MAP(0, 26)

#define NFC1_PIN NRF_GPIO_PIN_MAP(0, 9)
#define NFC2_PIN NRF_GPIO_PIN_MAP(0, 10)

#elif (LOCATECARD_V4_5)
/* Version inforamtion */
#define HW_VER "V4.5"
#define SW_VER "V3.x.x"

#elif (LOCATECARD_V5_3)
/* Version inforamtion */
#define HW_VER "V5.3"
#define SW_VER "V3.x.x"

/* UART Id */
#define UART_AIR724 1
#define UART_GPS 2

// #define NRF_GPIO_PIN_RESET NRF_GPIO_PIN_MAP(0, 18)

/* To LTE cat1 Air724 */
#define LTE_UART_TX_PIN         NRF_GPIO_PIN_MAP(1, 3)
#define LTE_UART_RX_PIN         NRF_GPIO_PIN_MAP(0, 31)
#define LTE_PWRKEY_PIN          NRF_GPIO_PIN_MAP(1, 11)
#define LTE_STATUS_PIN          NRF_GPIO_PIN_MAP(1, 4)
#define LTE_POWER_EN_PIN        NRF_GPIO_PIN_MAP(0, 26)

/* To GPS */

#define GNSS_UART_TX_PIN        NRF_GPIO_PIN_MAP(0, 14)
#define GNSS_UART_RX_PIN        NRF_GPIO_PIN_MAP(0, 15)
#define GNSS_PWRCTRL_PIN        NRF_GPIO_PIN_MAP(0, 11)
#define GNSS_FORCE_ON_PIN       NRF_GPIO_PIN_MAP(0, 13)

/* To Lora SX1268 */
#define SX1268_PWRCTRL_PIN      NRF_GPIO_PIN_MAP(1, 9)
#define SX1268_RESET_PIN        NRF_GPIO_PIN_MAP(1, 15)
//#define SX1268_TCXOEN_PIN     NRF_GPIO_PIN_MAP(0, 30)
#define SX1268_BUSY_PIN         NRF_GPIO_PIN_MAP(1, 14)

#define SX1268_DIO1_PIN         NRF_GPIO_PIN_MAP(1, 13)
#define SX1268_DIO2_PIN         NRF_GPIO_PIN_MAP(1, 122)
//#define SX1268_DIO3_PIN       NRF_GPIO_PIN_MAP(0, 28)
#define SX1268_ANT_SW           NRF_GPIO_PIN_MAP(0, 30)

#define SPI2_SCK_PIN            NRF_GPIO_PIN_MAP(0, 28)
#define SPI2_MISO_PIN           NRF_GPIO_PIN_MAP(0, 3)
#define SPI2_MOSI_PIN           NRF_GPIO_PIN_MAP(0, 2)
#define SX1268_CS_PIN           NRF_GPIO_PIN_MAP(0, 29)

/* I2C:  ICM20600 & CW2015 share one I2C, BMP280 use another */
#define TWI0_SCL_PIN            NRF_GPIO_PIN_MAP(0, 6)
#define TWI0_SDA_PIN            NRF_GPIO_PIN_MAP(0, 5)
#define TWI1_SCL_PIN            NRF_GPIO_PIN_MAP(0, 0)
#define TWI1_SDA_PIN            NRF_GPIO_PIN_MAP(0, 0)

/* LED, Button, Beep */
#define LED1_GREEN_PIN          NRF_GPIO_PIN_MAP(1, 7) // swap LED1 and LED2 pin as color. now LED1 blue,
#define LED2_BLUE_PIN           NRF_GPIO_PIN_MAP(1, 10)
#define LED3_RED_PIN            NRF_GPIO_PIN_MAP(1, 6)

#define BUTTON_PIN              NRF_GPIO_PIN_MAP(1, 5)
#define BEEP_PIN                NRF_GPIO_PIN_MAP(0, 16)

/* Charge */
#define ME4057_STDBY_PIN        NRF_GPIO_PIN_MAP(1,8)
#define ME4057_CHRG_PIN         NRF_GPIO_PIN_MAP(0, 8)
#define ME4057_ALRT_PIN         NRF_GPIO_PIN_MAP(0, 7)

#define ICM20600_INT            NRF_GPIO_PIN_MAP(0, 0)

//#define NFC1_PIN NRF_GPIO_PIN_MAP(0, 9)
//#define NFC2_PIN NRF_GPIO_PIN_MAP(0, 10)
#endif

#ifdef __cplusplus
}
#endif

#endif /* CUSTOM_BOARD_H */

```

```c
/*******************************************************************************
  locatorCard
*******************************************************************************/
#define GLOBALS 1
#include "main.h"

char SWInforamtion[64] = {0};
char DeviceName[64] = {0};
error_info_t *gFaultInfoPtr;

#if WDT_ENABLED

void wdt_event_handler(void)
{
}

void wdt_init(void)
{
  uint32_t err_code = NRF_SUCCESS;
  nrf_drv_wdt_config_t config = NRF_DRV_WDT_DEAFULT_CONFIG;

  /* 一个周期内 */
  config.reload_value = 5000;
  err_code = nrfx_wdt_init(&config, wdt_event_handler);
  APP_ERROR_CHECK(err_code);
  err_code = nrf_drv_wdt_channel_alloc(&global.m_channel_id);
  APP_ERROR_CHECK(err_code);
  nrf_drv_wdt_enable();
}

void wdt_feed(void)
{
  nrf_drv_wdt_channel_feed(global.m_channel_id);
}
#endif

static void ble_pass_uart_packet_handler(uint8_t type, uint8_t *data)
{
  uint32_t id;
  uint8_t tmpMac[6] = {0};
  Request *p_req = (Request *)data;
  Response response;

  //Fstorage_FlashContrl(APP_FSTORAGE_ADDR, FSTORAGE_READ, (uint32_t *)&appConfig, sizeof(appConfig));
  NRF_LOG_INFO("Receive BLE config Req, CMD:%d, parameter:0x%x", p_req->Cmd, p_req->Parameter);
  switch (type)
  {
  case RequestType:
  {
    response.Type = ResponseType;
    response.Cmd = p_req->Cmd;
    response.Parameter = p_req->Parameter;

    switch (p_req->Cmd)
    {
    case CMD_SET_ID:
      if (p_req->Parameter == 0) // read current ID
      {
        if (global.card_id[5] != 0) // use BLE MAC addr
        {
          response.Parameter = 0xFFFFFFFF;
        }
        else
        {
          response.Parameter = global.card_ci_id;
        }
      }
      else
      {
        id = p_req->Parameter;
        if ((id > 0) && (id < 0xFFFFFFFF))
        {
          global.card_ci_id = id;
          global.appconfigFlag = 1;
          tmpMac[0] = 0x0;
          tmpMac[1] = (id >> 24) & 0xFF;
          tmpMac[2] = (id >> 16) & 0xFF;
          tmpMac[3] = (id >> 8) & 0xFF;
          tmpMac[4] = (id >> 0) & 0xFF;
          tmpMac[5] = 0x0;
          memcpy(global.card_id, tmpMac, 6);
        }
        else
        {
          response.Parameter = 0;
        }
        NRF_LOG_INFO("Set ID req,  Id=%u", id);
      }

      break;
    case CMD_SET_RF_BASE:
      if (p_req->Parameter == 0) // read current base rf
      {
        response.Parameter = global.app_config.rf_base_freq;
      }
      else
      {
        if ((p_req->Parameter >= 425000000) && (p_req->Parameter <= 600000000))
        {
          global.app_config.rf_base_freq = p_req->Parameter;
          global.appconfigFlag = 1;
        }
        else
        {
          response.Parameter = 0;
        }
        NRF_LOG_INFO("Set freq req,  freq=%u", p_req->Parameter);
      }

      break;
    case CMD_SET_RF_STEP:
      if (p_req->Parameter == 0) // read current  rf step
      {
        response.Parameter = global.app_config.rf_freq_step;
      }
      else
      {
        if ((p_req->Parameter >= 1000) && (p_req->Parameter <= 6000))
        {
          global.app_config.rf_freq_step = p_req->Parameter;
          global.appconfigFlag = 1;
        }
        else
        {
          response.Parameter = 0;
        }
        NRF_LOG_INFO("Set RF step req,  step=%u", p_req->Parameter);
      }
      break;
    case CMD_SET_CHANEL_NUM:
      if (p_req->Parameter == 0) // read current channel num
      {
        response.Parameter = global.app_config.channel_num;
      }
      else
      {
        if ((p_req->Parameter >= 2) && (p_req->Parameter <= 10))
        {
          global.app_config.channel_num = p_req->Parameter;
          global.appconfigFlag = 1;
        }
        else
        {
          response.Parameter = 0;
        }
        NRF_LOG_INFO("Set CHANEL_NUM,  chanNum=%d", p_req->Parameter);
      }
      break;
    case CMD_SET_HW_VER:
      if (p_req->Parameter == 0)
      {
        response.Parameter = (global.card_info_config.hw_ver[2] << 16) | (global.card_info_config.hw_ver[1] << 8) | (global.card_info_config.hw_ver[0] << 0);
      }
      else
      { //Check byte order
        global.card_info_config.hw_ver[0] = (p_req->Parameter >> 0) & 0xFF;
        global.card_info_config.hw_ver[1] = (p_req->Parameter >> 8) & 0xFF;
        global.card_info_config.hw_ver[2] = (p_req->Parameter >> 16) & 0xFF;
        global.appconfigFlag = 1;
        NRF_LOG_INFO("Set HWVER ,  %d.%d.%d", global.card_info_config.hw_ver[0], global.card_info_config.hw_ver[1], global.card_info_config.hw_ver[2]);
      }
      break;

    case CMD_SET_TRIGGER_RESET:
      if (p_req->Parameter == 0)
      {
        response.Parameter = 0;
      }
      else
      {
        global.appconfigFlag = 2;
      }
      break;
    }
    send_data_to_server((uint8_t *)&response, sizeof(Response));
  }
  break;
  }
}

void init_global_param(void)
{
  uint8_t i;
  uint8_t tmp_data[6] = {0};
  uint32_t uicr_customer0;
  config_app_info_t appConfig;
  config_app_info_t idConfig;
  global.card_restart_flag = 1;

  global.rtt_lte_enable_flag = 1;
  global.rtt_gnss_enable_flag = 1;
  global.rtt_monitor_enable_flag = 1;

  global.card_info.slot_flag = RADIO_SLOT_NO;
  global.card_info.slot_no = 0;
  /* 设置为-1是为定时器第一次callbcak后 cur_slot_id++后，值为0 */
  global.cur_slot_id = -1;
  global.last_slot_id = 999;
  global.join_slot_id = -1;
  global.join_ack_slot_id = -10;
  global.ulData_slot_id = -1;
  global.ulData_sync_slot_id = -1;
  global.scan_start_id = -1;
  global.scan_freq_in_use = 470377000;
  /* 扫描到有效Tag */
  global.is_beacon_rssi_strong_flag = 0;

//  NRF_POWER->GPREGRET=0x01;
  global.gnss_reference_latitude = 0.0;
  global.gnss_reference_longitude = 0.0;
  global.epo_last_set_used_timestamp = 0;
  global.epo_last_set_stored_timestamp = 0;

  global.last_sync_ticks = 0;
  global.startJoin_time = 0;
  global.join_cnt = 0;
  global.scan_cnt = 0;
  global.lora_state = RADIO_NONE;

/* 二道门外基本上不会重启定位卡，所以初始化为二道门内 */
#if (1 == FANGYANG)
  global.boundary_state_current = BOUNDARY_INNER;
#else
  global.boundary_state_current = BOUNDARY_OUTER;
#endif

  global.second_door_out_times = 0;
  global.report_interval = REPORT_INTERVAL_MIN; //2seconds

  global.FPU_enable = 0;

#if ONLY_USE_LORA
  global.use_lora_or_Lte = LORA_TRANSMIT_STATE;
#else
  global.use_lora_or_Lte = LTE_TRANSMIT_STATE;
#endif

  global.startUseLora_tick = -10;

  global.indoor = 0;

  global.ota_flag = 0;
  global.sim_card[0] = 0;

  global.card_info.gw_info.rf_base_freq = RF_BASE_DEFAULT;
  global.card_info.gw_info.rf_freq_step = RF_STEP_DEFAULT;

  for (i = 0; i < NEIGHTBOR_NUM; i++)
  {
    global.card_info.gw_info.neighbor_channels[i] = i * GW_MAX_CHANNEL;
  }

  global.ble_pass_uart_recv_pd = CreatePDInstance(ble_pass_uart_packet_handler);

  /* comment it as now use ID */
  get_ble_mac_addr((char *)tmp_data, 0);
  for (i = 0; i < 6; i++)
  {
    global.ble_mac[i] = tmp_data[5 - i];
  }
  sprintf(DeviceName, "DWK-1_%02X%02X", global.ble_mac[4], global.ble_mac[5]);

  memset(&idConfig, 0, sizeof(idConfig));
  Fstorage_FlashContrl(APP_FSTORAGE_ADDR, FSTORAGE_READ, (uint32_t *)&idConfig, sizeof(idConfig));
  memset(&appConfig, 0, sizeof(appConfig));
  Fstorage_FlashContrl(APP_FSTORAGE_ADDR_NO_ID, FSTORAGE_READ, (uint32_t *)&appConfig, sizeof(appConfig));

  global.app_config = appConfig;
  global.card_info_config = idConfig;

  global.gnss_loc_type = global.app_config.gnss_loc_type == 0xff ? global.gnss_loc_type : global.app_config.gnss_loc_type;
  double latitude =
      (double)(global.app_config.reference_latitude * 1.0 / (1e+7));
  double longitude =
      (double)(global.app_config.reference_longitude * 1.0 / (1e+7));
  bool ret_position_validation = gnss_position_validation(latitude, longitude);
  if (ret_position_validation)
  {
    global.gnss_reference_latitude = latitude;
    global.gnss_reference_longitude = longitude;
  }

  if (idConfig.hw_ver[3] == 0xFF && idConfig.hw_ver[2] == 0xFF)
  {
    idConfig.hw_ver[0] = 0xFF;
    idConfig.hw_ver[1] = 0x00;
    idConfig.hw_ver[2] = 0x03;
    idConfig.hw_ver[3] = 0x04;
  }

  if (idConfig.sd_ver[3] == 0xFF && idConfig.sd_ver[2] == 0xFF)
  {
    idConfig.sd_ver[0] = 0xFF;
    idConfig.sd_ver[1] = 0x01;
    idConfig.sd_ver[2] = 0x00;
    idConfig.sd_ver[3] = 0x07;
  }

  if (idConfig.app_ver[3] == 0xFF && idConfig.app_ver[2] == 0xFF)
  {
    idConfig.app_ver[0] = 0xFF;
    idConfig.app_ver[1] = 0x01;
    idConfig.app_ver[2] = 0x01;
    idConfig.app_ver[3] = 0x01;
  }

  if (idConfig.boot_ver[3] == 0xFF && idConfig.boot_ver[2] == 0xFF)
  {
    idConfig.boot_ver[0] = 0xFF;
    idConfig.boot_ver[1] = 0x01;
    idConfig.boot_ver[2] = 0x01;
    idConfig.boot_ver[3] = 0x01;
  }

  memcpy(global.app_config.hw_ver, idConfig.hw_ver, sizeof(idConfig.hw_ver));
  memcpy(global.app_config.app_ver, idConfig.app_ver, sizeof(idConfig.app_ver));
  memcpy(global.app_config.sd_ver, idConfig.sd_ver, sizeof(idConfig.sd_ver));
  memcpy(global.app_config.boot_ver, idConfig.boot_ver, sizeof(idConfig.boot_ver));

  memcpy(global.card_info_config.hw_ver, idConfig.hw_ver, sizeof(idConfig.hw_ver));
  memcpy(global.card_info_config.app_ver, idConfig.app_ver, sizeof(idConfig.app_ver));
  memcpy(global.card_info_config.sd_ver, idConfig.sd_ver, sizeof(idConfig.sd_ver));
  memcpy(global.card_info_config.boot_ver, idConfig.boot_ver, sizeof(idConfig.boot_ver));

  uicr_customer0 = NRF_UICR->CUSTOMER[0];

  global.agnss_slot_timestamp = global.app_config.agnss_slot_timestamp;
  global.agnss_slot = (global.app_config.agnss_slot == 0 || global.app_config.agnss_slot == 0xffffffff) ? 3600 : global.app_config.agnss_slot;
  global.beacon_sleep_thred = (global.app_config.beacon_sleep_thred == 0 ||
                               global.app_config.beacon_sleep_thred == 0xffffffff)
                                  ? ((int32_t)-25)
                                  : global.app_config.beacon_sleep_thred;

  if (global.app_config.location_zone.count > 0 && global.app_config.location_zone.count <= 64)
  {
    for (int i = 0; i < global.app_config.location_zone.count; i++)
    {
      global.app_config.location_zone.latitudes[i] = (global.app_config.location_zone.latitudes[i] == 0xffffffff) ? 0 : global.app_config.location_zone.latitudes[i];
      global.app_config.location_zone.longitudes[i] = (global.app_config.location_zone.longitudes[i] == 0xffffffff) ? 0 : global.app_config.location_zone.longitudes[i];
    }
  }

  global.card_ci_id = idConfig.Id;
  if ((global.card_ci_id > 0) && (global.card_ci_id < 0xFFFFFFFF)) //如果FLASH内保存的ID字段不是初始值0x00或0xFF
  {
    tmp_data[0] = 0x0;
    tmp_data[1] = (global.card_ci_id >> 24) & 0xFF;
    tmp_data[2] = (global.card_ci_id >> 16) & 0xFF;
    tmp_data[3] = (global.card_ci_id >> 8) & 0xFF;
    tmp_data[4] = (global.card_ci_id >> 0) & 0xFF;
    tmp_data[5] = 0x0;
    memcpy(global.card_id, tmp_data, 6);
  }
  else if ((uicr_customer0 > 0) && (uicr_customer0 < 0xFFFFFFFF)) //向后兼容，原来部分卡设置了ID在UICR，如果NRF_UICR->CUSTOMER[0]内保存的ID字段不是初始值0x00或0xFF
  {
    tmp_data[0] = 0x0;
    tmp_data[1] = (uicr_customer0 >> 24) & 0xFF;
    tmp_data[2] = (uicr_customer0 >> 16) & 0xFF;
    tmp_data[3] = (uicr_customer0 >> 8) & 0xFF;
    tmp_data[4] = (uicr_customer0 >> 0) & 0xFF;
    tmp_data[5] = 0x0;
    memcpy(global.card_id, tmp_data, 6);
    global.card_ci_id = uicr_customer0;
    //      global.appconfigFlag = 1;// trigger to flash storage
  }
  else
  {
    // Not set ID yet, keep use BLE mac addr
  }

  if ((appConfig.rf_base_freq >= 425000000) && (appConfig.rf_base_freq <= 600000000))
  {
    global.app_config.rf_base_freq = appConfig.rf_base_freq;
  }
  else
  {
    global.app_config.rf_base_freq = RF_BASE_DEFAULT;
  }

  if ((appConfig.rf_freq_step >= 100) && (appConfig.rf_freq_step <= 6000)) //unit KHZ
  {
    global.app_config.rf_freq_step = appConfig.rf_freq_step;
  }
  else
  {
    global.app_config.rf_freq_step = RF_STEP_DEFAULT;
  }

  if ((appConfig.channel_num >= 2) && (appConfig.channel_num <= 16))
  {
    global.app_config.channel_num = appConfig.channel_num;
  }
  else
  {
    global.app_config.channel_num = GW_MAX_CHANNEL;
  }

  if ((appConfig.gw_num_lan >= 2) && (appConfig.gw_num_lan <= 16))
  {
    global.app_config.gw_num_lan = appConfig.gw_num_lan;
  }
  else
  {
    global.app_config.gw_num_lan = NEIGHTBOR_NUM;
  }

  global.slot_ticks = us2ticks(52 * 1000) + us2ticks(3 * 1000);
  global.toa_tick = us2ticks(44 * 1000);
  global.begin_tick_from_sync = 0;
  global.begin_tick_for_current_loop = 0;
  global.sync_rx_flag = false;
  global.lora_net_state = SYNC_NONE; // LORA状态
  global.last_sync_tick = 0;
  // 初始化LORA扫描窗口
  for (int i = 0; i < 8; i++)
  {
    global.lora_scan_channels[i] = 4 * i; // 默认为0,4,8,12,16,20,24,28
  }
  global.lora_scan_index = 0;

  global.cw2015.dev_addr = CW2015_SLAVE_ADDR;
  global.cw2015.read_buffer = twi0_read_buffer;
  global.cw2015.write_buffer = twi0_write_buffer;
  global.cw2015.delay_ms = platform_delay_ms;
  uint8_t cw2015_config[] =
      {
          0x15,
          0x7E,
          0x5E,
          0x59,
          0x57,
          0x54,
          0x54,
          0x52,
          0x52,
          0x4C,
          0x48,
          0x44,
          0x3F,
          0x38,
          0x32,
          0x30,
          0x2D,
          0x2B,
          0x2A,
          0x2F,
          0x36,
          0x36,
          0x40,
          0x27,
          0x2A,
          0xA4,
          0x0A,
          0x3E,
          0x25,
          0x45,
          0x4D,
          0x52,
          0x61,
          0x6D,
          0x6C,
          0x6D,
          0x4B,
          0x24,
          0x90,
          0x39,
          0x0A,
          0x33,
          0x17,
          0x41,
          0x70,
          0x9B,
          0xB3,
          0x17,
          0x36,
          0x76,
          0x9A,
          0xBA,
          0x80,
          0xCB,
          0xF8,
          0xCB,
          0x2F,
          0x00,
          0x64,
          0xA5,
          0xB5,
          0x1C,
          0x20,
          0x11,
      };

  memcpy(global.cw2015_config_info, cw2015_config, 64);

  /* Assign device I2C address based on the status of SDO pin (GND for PRIMARY(0x76) & VDD for SECONDARY(0x77)) */
  global.bmp280.dev_id = BMP280_I2C_ADDR_PRIM;
  /* Select the interface mode as I2C */
  global.bmp280.intf = BMP280_I2C_INTF;
  global.bmp280.read_buffer = twi1_read_buffer;
  global.bmp280.write_buffer = twi1_write_buffer;
  global.bmp280.delay_ms = platform_delay_ms;

  global.icm20600.dev_id = ICM20600_I2C_ADDR1;
  global.icm20600.read_buffer = twi0_read_buffer;
  global.icm20600.write_buffer = twi0_write_buffer;
  global.icm20600.delay_ms = platform_delay_ms;

  global.timestamp_sync = 0;
  global.timestamp_sync_tick = 0;

  global.icm20600_data_ready = false;
  global.is_freefall = false;
  global.is_quickdrop = false;
  global.is_charging = false;
  global.freefall_count = 0;
  global.quickdrop_count = 0;
}

/**@brief A function which is hooked to idle task.
 * @note Idle hook must be enabled in FreeRTOS configuration (configUSE_IDLE_HOOK).
 */
void vApplicationIdleHook(void)
{
  //#if NRF_LOG_ENABLED
  //  vTaskResume(global.task_handle_log);
  //#endif
}

void vApplicationMallocFailedHook(void)
{
  NRF_LOG_INFO("vApplicationMallocFailedHook");
}

void vApplicationStackOverflowHook(TaskHandle_t xTask, char *pcTaskName)
{
  NRF_LOG_INFO("vApplicationStackOverflowHook %s", pcTaskName);
}

#if NRF_LOG_ENABLED
static void task_logger(void *arg)
{
  UNUSED_PARAMETER(arg);

  while (1)
  {
    NRF_LOG_FLUSH();
    vTaskDelay(us2ticks(100));
  }
}
#endif

static uint8_t random_vector_generate(uint8_t *p_buff, uint8_t size)
{
  uint32_t err_code;
  uint8_t available;

  nrf_drv_rng_bytes_available(&available);
  uint8_t length = MIN(size, available);

  err_code = nrf_drv_rng_rand(p_buff, length);
  APP_ERROR_CHECK(err_code);

  return length;
}

uint8_t NRF_GET_RNG(void)
{
  uint8_t p_buff[RANDOM_BUFF_SIZE];

  random_vector_generate(p_buff, RANDOM_BUFF_SIZE);
  return p_buff[0];
}

/**
 * Function for configuring UICR_REGOUT0 register
 * to set GPIO output voltage to 3.0V.
 */
static void gpio_output_voltage_setup(void)
{
  // Configure UICR_REGOUT0 register only if it is set to default value.
  if ((NRF_UICR->REGOUT0 & UICR_REGOUT0_VOUT_Msk) ==
      (UICR_REGOUT0_VOUT_DEFAULT << UICR_REGOUT0_VOUT_Pos))
  {
    NRF_NVMC->CONFIG = NVMC_CONFIG_WEN_Wen;
    while (NRF_NVMC->READY == NVMC_READY_READY_Busy)
    {
    }

    NRF_UICR->REGOUT0 = (NRF_UICR->REGOUT0 & ~((uint32_t)UICR_REGOUT0_VOUT_Msk)) |
                        (UICR_REGOUT0_VOUT_3V3 << UICR_REGOUT0_VOUT_Pos);

    NRF_NVMC->CONFIG = NVMC_CONFIG_WEN_Ren;
    while (NRF_NVMC->READY == NVMC_READY_READY_Busy)
    {
    }

    NRF_LOG_ERROR("[voltage] [setup] NVIC_SystemReset");
    platform_delay_ms(5000);
    // System reset is needed to update UICR registers.
    NVIC_SystemReset();
  }
}

void FPU_IRQHandler(void)
{
  uint32_t *fpscr = (uint32_t *)(FPU->FPCAR + 0x40);
  (void)__get_FPSCR();

  *fpscr = *fpscr & ~(FPU_EXCEPTION_MASK);
}

/**
 * Function is implemented as weak so that it can be overwritten by custom application error handler
 * when needed.
 */
void app_error_fault_handler(uint32_t id, uint32_t pc, uint32_t info)
{

  __disable_irq();

  gFaultInfoPtr = (error_info_t *)info;
  NRF_LOG_ERROR("ERROR %u [%s] at %s:%u\r\nPC at: 0x%08x",
                gFaultInfoPtr->err_code,
                nrf_strerror_get(gFaultInfoPtr->err_code),
                gFaultInfoPtr->p_file_name,
                gFaultInfoPtr->line_num,
                pc);

  NRF_LOG_FINAL_FLUSH();

  /* GPS to backup mode */
  gnss_power_disable();

  NRF_LOG_ERROR("[APP] [ERROR] NVIC_SystemReset");
  platform_delay_ms(5000);
  NVIC_SystemReset();

#ifndef DEBUG
  NRF_LOG_ERROR("Fatal error");
#else
  switch (id)
  {
#if defined(SOFTDEVICE_PRESENT) && SOFTDEVICE_PRESENT
  case NRF_FAULT_ID_SD_ASSERT:
    NRF_LOG_ERROR("SOFTDEVICE: ASSERTION FAILED");
    break;
  case NRF_FAULT_ID_APP_MEMACC:
    NRF_LOG_ERROR("SOFTDEVICE: INVALID MEMORY ACCESS");
    break;
#endif
  case NRF_FAULT_ID_SDK_ASSERT:
  {
    assert_info_t *p_info = (assert_info_t *)info;
    NRF_LOG_ERROR("ASSERTION FAILED at %s:%u",
                  p_info->p_file_name,
                  p_info->line_num);
    break;
  }
  case NRF_FAULT_ID_SDK_ERROR:
  {
    error_info_t *p_info = (error_info_t *)info;
    NRF_LOG_ERROR("ERROR %u [%s] at %s:%u\r\nPC at: 0x%08x",
                  p_info->err_code,
                  nrf_strerror_get(p_info->err_code),
                  p_info->p_file_name,
                  p_info->line_num,
                  pc);
    NRF_LOG_ERROR("End of error report");
    break;
  }
  default:
    NRF_LOG_ERROR("UNKNOWN FAULT at 0x%08X", pc);
    break;
  }
#endif

  NRF_BREAKPOINT_COND;
  // On assert, the system can only recover with a reset.

#ifndef DEBUG
  NRF_LOG_ERROR("[APP] [ERROR] [DEBUG] NVIC_SystemReset");
  platform_delay_ms(5000);
  NVIC_SystemReset();
#else
  app_error_save_and_stop(id, pc, info);
#endif // DEBUG
}
void task_rtt_log(void *arg)
{
    uint32_t current_tick = xTaskGetTickCount();
    uint32_t next_delay_tick = ms2ticks(100);
    char rtt_read_str[100] = {0};
    uint32_t count = 0;
    NRF_LOG_INFO("Dog_Count:~%d$", count);
    for (;;)
    {
        vTaskDelayUntil(&current_tick, next_delay_tick);
        count++;
        if (((count % 50) == 0))
        {
            NRF_LOG_INFO("Dog_Count:~%d$", count);
        }
        if(((count % 10) == 0))
        {
            nrf_gpio_pin_toggle(LED_RED_PIN);
        }
    }
}
int main(void)
{
  global.reset_source = NRF_POWER->RESETREAS;
  /* Clear reset reason by writting 1 */
  NRF_POWER->RESETREAS = NRF_POWER->RESETREAS;

  bool erase_bonds;
  ret_code_t err_code;
  uint8_t ret;

  /* 初始化必要外设. */
  err_code = nrf_drv_clock_init();
  APP_ERROR_CHECK(err_code);

  /* 起振高速晶振，不启动高速晶振，减少功耗，@dhzhang-2020/5/11 */
  //  nrf_drv_clock_hfclk_request(NULL);
  //  while (!nrf_drv_clock_hfclk_is_running())
  //  {
  //    __NOP();
  //  }
  /* default 1.8V,  configure to 3.3V */
  gpio_output_voltage_setup();

  //  err_code = nrf_drv_rng_init (NULL);
  //  APP_ERROR_CHECK(err_code);

  err_code = nrfx_gpiote_init();
  APP_ERROR_CHECK(err_code);

  // handle FPU in case of increasing power consuming
  NVIC_SetPriority(FPU_IRQn, APP_IRQ_PRIORITY_LOW);
  NVIC_EnableIRQ(FPU_IRQn);

  Fstorage_FlashInit();
  init_global_param();

  sprintf(SWInforamtion, "%s_Build_%s_%s", SW_VER, __DATE__, __TIME__);

  board_gpio_init();

#if WDT_ENABLED
  wdt_init();
#endif

#if NRF_LOG_ENABLED
  err_code = NRF_LOG_INIT(xTaskGetTickCount, configTICK_RATE_HZ);
  APP_ERROR_CHECK(err_code);
  NRF_LOG_DEFAULT_BACKENDS_INIT();

  if (pdPASS != xTaskCreate(task_logger, "LOGGER", 384, NULL, 1,
                            &global.task_handle_log))
  {
    APP_ERROR_HANDLER(NRF_ERROR_NO_MEM);
  }
  /*Startup sysview */
//  SEGGER_SYSVIEW_Conf();
#endif

  /* 设置低功耗模式为深度睡眠模式  CORTEX M4  */
//  SCB->SCR |= SCB_SCR_SLEEPDEEP_Msk;

#if BLE_EN
  m_ble_init();
  nrf_sdh_freertos_init(advertising_start, &erase_bonds);
//  nrf_sdh_freertos_init (NULL, &erase_bonds);
#endif /* BLE_EN */

//  ret = sd_power_dcdc_mode_set(NRF_POWER_DCDC_ENABLE);
  NRF_LOG_INFO("Power mode set :%d", ret)

  global.semaphone_handle_gnss_scene_operation = xSemaphoreCreateMutex();
  ASSERT(global.semaphone_handle_gnss_scene_operation)

  global.semaphone_handle_epo_xfer_complete = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_epo_xfer_complete)

  global.semaphone_handle_lte_rx_xfer_complete = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_lte_rx_xfer_complete)

  global.semaphone_handle_lte_power_control_cmd_ack = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_lte_power_control_cmd_ack)

  global.semaphone_handle_udp_hb_ack = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_udp_hb_ack)

  global.semphore_uarte_gnss_tx_done = xSemaphoreCreateBinary();
  ASSERT(global.semphore_uarte_gnss_tx_done)

  global.semphore_uarte_lte_tx_done = xSemaphoreCreateBinary();
  ASSERT(global.semphore_uarte_lte_tx_done)

  global.semaphone_handle_twi0 = xSemaphoreCreateMutex();
  ASSERT(global.semaphone_handle_twi0)

  global.semaphone_handle_twi0_xfer_complete = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_twi0_xfer_complete)

  global.semaphone_handle_twi1 = xSemaphoreCreateMutex();
  ASSERT(global.semaphone_handle_twi1)

  global.semaphone_handle_twi1_xfer_complete = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_twi1_xfer_complete)

  global.semaphone_handle_spi2 = xSemaphoreCreateMutex();
  ASSERT(global.semaphone_handle_spi2)

  global.semaphone_handle_spi2_xfer_complete = xSemaphoreCreateBinary();
  ASSERT(global.semaphone_handle_spi2_xfer_complete)

  global.queue_uarte_gnss_tx_msg = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_uarte_gnss_tx_msg)

  global.queue_uarte_gnss_rx_msg = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_uarte_gnss_rx_msg)

  global.queue_uarte_gnss_resp_msg = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_uarte_gnss_resp_msg)

  global.queue_uarte_lte_tx_msg = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_uarte_lte_tx_msg)

  global.queue_uarte_lte_rx_msg = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_uarte_lte_rx_msg)

  global.queue_uarte_lte_resp_msg = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_uarte_lte_resp_msg)

  global.queue_handle_lte_process = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_handle_lte_process)

  global.queue_handle_gnss_process = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_handle_gnss_process)

  /* LORA TX Done Semaphore and TX RX queue */
  global.semphore_lora_tx_done = xSemaphoreCreateBinary();
  ASSERT(global.semphore_lora_tx_done)

  global.queue_handle_lora_process = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_handle_lora_process)

  global.queue_lora_tx_msg = xQueueCreate(5, sizeof(msg_t));
  ASSERT(global.queue_lora_tx_msg)

  global.queue_lora_rx_msg = xQueueCreate(5, sizeof(msg_t));
  ASSERT(global.queue_lora_rx_msg)

  global.queue_lora_dio1_msg = xQueueCreate(5, sizeof(msg_t));
  ASSERT(global.queue_lora_dio1_msg)

  global.queue_lora_dio2_msg = xQueueCreate(5, sizeof(msg_t));
  ASSERT(global.queue_lora_dio2_msg)

  global.queue_handle_lora_tx = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_handle_lora_tx)

  global.queue_handle_lora_rx = xQueueCreate(20, sizeof(msg_t));
  ASSERT(global.queue_handle_lora_rx)

  global.timer_handle_epo = xTimerCreate("timer_epo",
                                         ms2ticks(5000),
                                         pdFALSE,
                                         NULL,
                                         epo_download_handler);
  ASSERT(global.timer_handle_epo)

  global.timer_handle_gnss_rx_timeout = xTimerCreate(
      "gnss_rx_timeout", ms2ticks(300), pdFALSE,
      NULL,
      timer_handler_gnss_rx_timeout);
  ASSERT(global.timer_handle_gnss_rx_timeout)

  global.timer_handle_lte_rx_timeout = xTimerCreate(
      "lte_rx_timeout", ms2ticks(300), pdFALSE,
      NULL,
      timer_handler_lte_rx_timeout);
  ASSERT(global.timer_handle_lte_rx_timeout)

  global.timer_handle_key = xTimerCreate("KEY", ms2ticks(500), pdFALSE,
                                         NULL,
                                         key_timer_handler);
  ASSERT(global.timer_handle_key)

  global.timer_handle_sos = xTimerCreate("SOS", ms2ticks(500), pdFALSE,
                                         NULL,
                                         sos_timer_handler);
  ASSERT(global.timer_handle_sos)

  global.timer_handle_key_cnt = xTimerCreate("key_cnt_process", ms2ticks(200),
                                             pdFALSE,
                                             NULL,
                                             key_pressed_cnt_handler);
  ASSERT(global.timer_handle_key_cnt)

  global.timer_handle_led_green_off = xTimerCreate("led_green_off",
                                                   ms2ticks(450),
                                                   pdFALSE,
                                                   NULL,
                                                   led_green_off_handler);
  ASSERT(global.timer_handle_led_green_off)

  global.timer_handle_led_blue_off = xTimerCreate("led_blue_off",
                                                  ms2ticks(10),
                                                  pdFALSE,
                                                  NULL,
                                                  led_blue_off_handler);
  ASSERT(global.timer_handle_led_blue_off)

  global.timer_handle_led_red_off = xTimerCreate("led_red_off",
                                                 ms2ticks(10), pdFALSE,
                                                 NULL,
                                                 led_red_off_handler);
  ASSERT(global.timer_handle_led_red_off)

  global.timer_handle_radio_first = xTimerCreate("radio_first_timer",
                                                 ms2ticks(50),
                                                 pdFALSE,
                                                 NULL,
                                                 radio_first_timer_handler);
  ASSERT(global.timer_handle_radio_first)

  global.timer_handle_radio_delay = xTimerCreate("radio_delay_timer",
                                                 ms2ticks(2),
                                                 pdFALSE,
                                                 NULL,
                                                 radio_delay_timer_handler);
  ASSERT(global.timer_handle_radio_delay)

  global.timer_handle_radio_secon = xTimerCreate("radio_secon_timer",
                                                 ms2ticks(20),
                                                 pdFALSE,
                                                 NULL,
                                                 radio_secon_timer_handler);
  ASSERT(global.timer_handle_radio_secon)

#if 0
  UNUSED_VARIABLE(
      xTaskCreate(task_gnss_process, "gnss_process", 512, NULL, 5, &global.task_handle_gnss_process));

  UNUSED_VARIABLE(
      xTaskCreate(task_uarte_gnss_tx, "uarte_gnss_tx", 1024, NULL, 8, &global.task_handle_gnss_uarte_tx));

  UNUSED_VARIABLE(
      xTaskCreate(task_uarte_gnss_rx, "uarte_gnss_rx", 4096, NULL, 10, &global.task_handle_gnss_uarte_rx));
#endif
#if 0
  UNUSED_VARIABLE(
      xTaskCreate(lte_task, "lte_process", 2048, NULL, 6, &global.task_handle_lte_process));
  
  UNUSED_VARIABLE(
      xTaskCreate(task_uarte_lte_tx, "uarte_lte_tx", 1024, NULL, 7, &global.task_handle_lte_uarte_tx));

  UNUSED_VARIABLE(
      xTaskCreate(task_uarte_lte_rx, "uarte_lte_rx", 4096, NULL, 9, &global.task_handle_lte_uarte_rx));
#endif

#if 0
   UNUSED_VARIABLE(
       xTaskCreate(task_lora_tx, "lora_tx", 256, NULL, 4, &global.task_handle_lora_tx));

   UNUSED_VARIABLE(
       xTaskCreate(task_lora_dio1, "lora_dio1", 256, NULL, 14, &global.task_handle_lora_dio1));

   UNUSED_VARIABLE(
       xTaskCreate(task_lora_dio2, "lora_dio2", 256, NULL, 13, &global.task_handle_lora_dio2));

   UNUSED_VARIABLE(
       xTaskCreate(task_lora_rx, "lora_rx", 256, NULL, 12, &global.task_handle_lora_rx));

   UNUSED_VARIABLE(
         xTaskCreate(task_lora_process, "lora_process", 512, NULL, 11, &global.task_handle_lora_process));
#endif

#if 0
  UNUSED_VARIABLE(
      xTaskCreate(task_lora_tx_li, "lora_tx_li", 256, NULL, 4, &global.task_handle_lora_tx_li));

  UNUSED_VARIABLE(
      xTaskCreate(task_lora_dio1_li, "lora_dio1_li", 256, NULL, 14, &global.task_handle_lora_dio1_li));

  //  UNUSED_VARIABLE(
  //      xTaskCreate(task_lora_dio2_li, "lora_dio2_li", 256, NULL, 13, &global.task_handle_lora_dio2_li));



#endif
#if 0
  UNUSED_VARIABLE(
      xTaskCreate(task_monitor, "monitor", 512, NULL, 3, &global.task_handle_monitor));

  UNUSED_VARIABLE(
       xTaskCreate(task_watch_dog, "watch_dog_process", 128, NULL, 1, &global.task_handle_watch_dog));
#endif



  UNUSED_VARIABLE(
      xTaskCreate(task_rtt_log, "rtt_log_task", 256, NULL, 1, &global.task_handle_rtt_log));

  NRF_LOG_INFO("powergw start");

  global.freeRTOS_on = 1;
  // Start FreeRTOS scheduler.
  vTaskStartScheduler();

  for (;;)
  {
    APP_ERROR_HANDLER(NRF_ERROR_FORBIDDEN);
  }
}

```



```c
#include "user_lora.h"

// extern task_queue_item_t lora_tx_item;
// extern task_queue_item_t lora_rx_item;
// extern task_queue_item_t lora_process_item;
extern scan_snr_t ch_scan_snr[NEIGHTBOR_NUM];
extern user_task_dev_t user_task_dev;

void lora_send_location_info(void);
static void lora_ul_data_producer(lora_location_t *out_lora_location);

bool outside_location_area = true; // 是否在定位区域外
uint32_t last_motion_uptime = 0;   // 最后一次检测到震动的时间
uint32_t timestamp = 0;            // 1970.01.01开始计算的时间，秒为单位，开机后通过4G和GNSS模块对时，每6小时校准一次
uint32_t agnss_slot = 0;           // AGNSS时间片，以秒为单位，开机后通过向服务器请求一次，未做配置

uint16_t ul_slot_id = 0; // 用来上报数据的时间片，0表示未设置，由基站分配确定，并且满足如下条件 (ul_slot_id % 5 > 0) && (ul_slot_id < 55) && (ul_slot_id % 15 < 10)

bool is_valid_ul_slot_id(uint16_t id)
{
  return (ul_slot_id % 5 > 0) && (ul_slot_id % 60 < 55) && (ul_slot_id % 15 < 10);
}

bool is_valid_scan_channel(uint8_t channel)
{
  return (channel % 4 == 0) && (channel / 4) < 8;
}

// uint8_t scan_first_channel(int32_t current_slod_id, uint8_t[] channels, uint16_t* next_slot_step)
//{
//   return 0;
// }

uint16_t scan_next_channel()
{
}

int32_t tdma_sync(uint32_t current_tick, int32_t current_slot_id,
                  int32_t *next_delay_tick)
{
  if (global.sync_rx_flag)
  {
    // 确保仅进入一次
    global.sync_rx_flag = false;
    global.begin_tick_for_current_loop = current_tick - current_slot_id * global.slot_ticks;
    int32_t sync_offset_tick = (int32_t)(global.begin_tick_from_sync - global.begin_tick_for_current_loop) % (int32_t)global.slot_ticks;
    int32_t sync_offset_slot = (int32_t)(global.begin_tick_for_current_loop - global.begin_tick_from_sync) / (int32_t)global.slot_ticks;
    // 确保延迟值为正
    if (*next_delay_tick + sync_offset_tick > 0)
    {
      global.last_sync_tick = current_tick;
      // 完成同步
      current_slot_id += sync_offset_slot;
      // 确保是在0~59区间
      current_slot_id = (current_slot_id + 60) % 60;
      *next_delay_tick += sync_offset_tick;
      if (global.lora_net_state == SYNC_NONE)
      {
        global.lora_net_state = SYNC_OK;
      }
    }
    else
    {
      // 在下一次唤醒时候继续同步
      //              global.sync_rx_flag = true;
    }
  }
  return current_slot_id;
}

void lora_rx_done(uint8_t *payload, uint16_t len, int8_t rssi, int8_t snr)
{
  NRF_LOG_ERROR("lora_rx_done ------------------------------------------ buf_data:0x%02x",payload[0]);
  // 将SX126X设置进入standby降低功耗
  sx126x_set_standby(&global.sx1268, SX126X_STDBY_RC);

  dl_sync0_t *p_dl_sync0;
  dl_sync1_t *p_dl_sync1;
  dl_sync2_t *p_dl_sync2;
  if (payload[0] == DL_SYNC0 && len == sizeof(dl_sync0_t))
  {
    p_dl_sync0 = payload;
    mark_sync_rx(&global.sx1268.rx_done_tick, p_dl_sync0->sync_index * 5);
  }
  else if (payload[0] == DL_SYNC1 && len == sizeof(dl_sync1_t))
  {
    p_dl_sync1 = payload;
    mark_sync_rx(&global.sx1268.rx_done_tick, p_dl_sync1->sync_index * 5);
  }
  else if (payload[0] == DL_SYNC2 && len == sizeof(dl_sync2_t))
  {
    p_dl_sync2 = payload;
  }
}

void lora_tx_done()
{
  xTaskNotifyGive(global.task_handle_lora_tx_li);
  // 发送完成后进入sleep模式，降低功耗
  sx126x_set_sleep_warm_start(&global.sx1268);
  NRF_LOG_INFO("SX1268 tx done.");
}

void lora_tx_timeout()
{
  // 发送完成后进入sleep模式，降低功耗，一般不会进入到这里
  // sx126x_set_sleep_warm_start (&global.sx1268);
}
#if 1
void task_lora_dio1()
{
  msg_t msg;
  msg.buf = NULL;
  while (true)
  {
    if (pdPASS == xQueueReceive(global.queue_lora_dio1_msg, &msg, portMAX_DELAY))
    {
      global.sx1268.dio1_tick = msg.tick;
      sx126x_dio1_process(&global.sx1268);
    }
  }
}

void task_lora_dio2()
{
  msg_t msg;
  msg.buf = NULL;
  while (true)
  {
    if (pdPASS == xQueueReceive(global.queue_lora_dio2_msg, &msg, portMAX_DELAY))
    {
      global.sx1268.dio2_tick = msg.tick;
      sx126x_dio2_process(&global.sx1268);
    }
  }
}
#endif

void lora_tx(uint8_t *buf, uint16_t len)
{
  msg_t msg;
  msg.buf = NULL;
  if (len > 0)
  {
    msg.buf = pvMalloc(len);
    if (msg.buf)
    {
      xQueueSend(global.queue_lora_tx_msg, &msg, portMAX_DELAY);
    }
  }
}

void task_lora_tx2()
{
  msg_t msg;
  msg.buf = NULL;
  while (true)
  {
    /*
     * 从LORA发送队列中获取内容，并进行处理，发送完成等待发送完成信号量(binarySemaphore)
     */
    if (pdTRUE == xQueueReceive(global.queue_lora_tx_msg, &msg,
                                portMAX_DELAY))
    {
      sx126x_tx(&global.sx1268, msg.buf, msg.buf_len, 50);
      if (pdTRUE == xSemaphoreTake(global.semphore_lora_tx_done, portMAX_DELAY))
      {
        // Tx Complete or Timeout
      }
      vPortFree(msg.buf);
    }
  }
}

void mark_sync_rx(uint32_t rx_tick, uint8_t slot_from_sync)
{
  global.begin_tick_from_sync = rx_tick - global.toa_tick - slot_from_sync * global.slot_ticks;
  global.sync_rx_flag = true;
}

static void lora_ul_data_producer(lora_location_t *out_lora_location)
{
  out_lora_location->type = UL_DATA;
  memcpy(out_lora_location->card_id, global.card_id, sizeof(global.card_id));
  out_lora_location->status[0] = global.status;
  for (uint8_t i = 0; i < BLE_BEACON_NUM; i++)
  {
    out_lora_location->beacons[i].voltage = global.ble_beacons_buff[i].voltage;
    out_lora_location->beacons[i].major = global.ble_beacons_buff[i].major;
    out_lora_location->beacons[i].minor = global.ble_beacons_buff[i].minor;
    out_lora_location->beacons[i].rssi_offset = global.ble_beacons_buff[i].offset;

    out_lora_location->status[1] |= ((global.ble_beacons_buff[i].indoor & 0x01) << (i));
    out_lora_location->status[1] |= ((global.ble_beacons_buff[i].sleep_flag & 0x01) << (4 + i));
    NRF_LOG_INFO("[lora_ul_data_producer] major: %4d, minor: %4d", out_lora_location->beacons[i].major, global.ble_beacons_buff[i].minor);
  }
  out_lora_location->battery_level = global.battery_level;
  out_lora_location->temperature = global.temperature / 100;
  out_lora_location->barometer = global.pressure / 10;
  out_lora_location->timestamp = get_cb_uptime(xTaskGetTickCount);
  NRF_LOG_WARNING("[lora_ul_data_producer]  Timestamp: %u, battery_level: %d",
                  out_lora_location->timestamp, out_lora_location->battery_level);
}

void lora_send_location_info(void)
{
  static uint8_t tx_count = 0;
  bool ret = false;
  msg_t lora_process_msg = {.buf = NULL};
  bool ble_available = beacon_ul_data_producer(global.ble_beacons_buff);
  if (ble_available)
  {
    lora_location_t lora_location_buff = {0};
    lora_ul_data_producer(&lora_location_buff);
    memcpy(global.ble_beacons_buff_ul, global.ble_beacons_buff, sizeof(global.ble_beacons_buff));
    memcpy(&global.nearest_max_rssi_beacon_ul, &global.nearest_max_rssi_beacon, sizeof(global.nearest_max_rssi_beacon_ul));
    lora_process_msg.type = RADIO_TX;
    lora_process_msg.buf_len = sizeof(lora_location_t);
    lora_process_msg.tick = xTaskGetTickCount();
    lora_process_msg.buf = pvPortMalloc(lora_process_msg.buf_len);
    memcpy(lora_process_msg.buf, &lora_location_buff, sizeof(lora_location_buff));
    // NRF_LOG_INFO("[lora_send_location_info] Send data length: %d", lora_process_msg.buf_len);
    // NRF_LOG_HEXDUMP_INFO(lora_process_msg.buf,lora_process_msg.buf_len);
    if (lora_process_msg.buf)
    {
      xQueueSend(global.queue_handle_lora_tx, &lora_process_msg, portMAX_DELAY);
    }
    if ((++tx_count) % 3 == 0)
    {
      nrf_gpio_pin_clear(LED_BLUE_PIN);
      vTaskDelay(ms2ticks(100));
      nrf_gpio_pin_set(LED_BLUE_PIN);
    }
  }
}

void task_lora_process(void *arg)
{
  ulTaskNotifyTake(pdTRUE, portMAX_DELAY);

  global.sx1268.device_id = SX1268_DEVICE_ID;
  global.sx1268.read_buffer = spi2_read_buffer;
  global.sx1268.write_buffer = spi2_write_buffer;
  global.sx1268.write_read_buffer = spi2_write_read_buffer;
  global.sx1268.delay_ms = platform_delay_ms;
  global.sx1268.wait_on_busy = sx126x_wait_on_busy;
  global.sx1268.cs_high = sx1268_cs_high;
  global.sx1268.cs_low = sx1268_cs_low;
  global.sx1268.rst_high = sx1268_rst_high;
  global.sx1268.rst_low = sx1268_rst_low;
  global.sx1268.ant_sw_tx = sx1268_ant_sw_tx;
  global.sx1268.ant_sw_rx = sx1268_ant_sw_rx;

  global.sx1268.tx_power = 22;
  global.sx1268.symble_timeout = 5;
  global.sx1268.rx_continuous = true;
  global.sx1268.callbacks.rxDone = lora_rx_done;
  global.sx1268.callbacks.txDone = lora_tx_done;
  global.sx1268.callbacks.rxHeaderDone = lora_rx_done;
  
  global.sx1268.callbacks.txTimeout = lora_tx_timeout;

  global.sx1268.modulation_params.PacketType = SX126X_PACKET_TYPE_LORA;
  global.sx1268.modulation_params.Params.LoRa.Bandwidth = SX126X_LORA_BW_250;
  global.sx1268.modulation_params.Params.LoRa.CodingRate = SX126X_LORA_CR_4_5;
  global.sx1268.modulation_params.Params.LoRa.LowDatarateOptimize = 0;
  global.sx1268.modulation_params.Params.LoRa.SpreadingFactor = LORA_SF7;

  global.sx1268.packet_params.PacketType = SX126X_PACKET_TYPE_LORA;
  global.sx1268.packet_params.Params.LoRa.HeaderType = SX126X_LORA_PACKET_IMPLICIT;
  global.sx1268.packet_params.Params.LoRa.CrcMode = SX126X_LORA_CRC_ON;
  global.sx1268.packet_params.Params.LoRa.PayloadLength = 49;
  global.sx1268.packet_params.Params.LoRa.InvertIQ = SX126X_LORA_IQ_NORMAL;
  global.sx1268.packet_params.Params.LoRa.PreambleLength = 8;

  BaseType_t ret_code;
  msg_t lora_process_msg = {.buf = NULL};

  int32_t current_slot_id = 0; // 当前slot id
  uint16_t next_slot_step = 1; // 下一个slot步进
  uint32_t current_tick = 0;   // 当前tick
  int32_t next_delay_tick = 0; // 下一个TaskDelayUntil的tick;
  int32_t sync_offset_tick = 0;
  int32_t sync_offset_slot = 0;

  current_tick = xTaskGetTickCount();
  ul_slot_id = 0;
  current_slot_id = 0;
  next_slot_step = 1;
  uint8_t checked_channel_nums = 0;

  sx1268_gpio_init();
  spi2_init();
  if(sx126x_init(&global.sx1268))
  {
    global.lora_li = 1;
  }
  
  sx126x_set_standby(&global.sx1268, SX126X_STDBY_RC);
  sx126x_set_rf_frequency(&global.sx1268, 470377000);
  sx126x_set_standby(&global.sx1268, SX126X_STDBY_RC);
  // sx126x_set_sleep_warm_start (&global.sx1268);
  sx126x_rx(&global.sx1268, 0);

  xTaskNotifyGive(global.task_handle_lora_rx);
  xTaskNotifyGive(global.task_handle_lora_tx);
  
  xTaskNotifyGive(global.task_handle_lora_tx_li);

  user_task_dev.report_location_info_action = lora_send_location_info;
  while (1)
  {
    ret_code = xQueueReceive(global.queue_handle_lora_process,
                             &lora_process_msg, portMAX_DELAY);

    //    next_delay_tick = global.slot_ticks * next_slot_step - us2ticks (3 * 1000);
    //
    //    current_slot_id = tdma_sync (current_tick, current_slot_id,
    //                                 &next_delay_tick);
    //
    //    vTaskDelayUntil (&current_tick, next_delay_tick);
    //
    //    // 某一个slot的后半部分开始，大部分同步应该发生在此
    //    next_delay_tick = us2ticks (3 * 1000);
    //
    //    current_slot_id = tdma_sync (current_tick, current_slot_id,
    //                                 &next_delay_tick);
    //
    ////      超过3个LOOP没有收到同步信号，状态切换为SYNC_NONE
    //    if (current_tick - global.last_sync_tick > global.slot_ticks * 60 * 3)
    //    {
    //      global.lora_net_state = SYNC_NONE;
    //    }
    //    if (SYNC_NONE == global.lora_net_state)
    //    {
    //      uint8_t scan_channel = global.lora_scan_channels[global.lora_scan_index];
    //      if (is_valid_scan_channel (scan_channel))
    //      {
    //
    //      }
    //    }
    //    vTaskDelayUntil (&current_tick, next_delay_tick);
    //    // 更新 current_slot_id
    //    current_slot_id = (current_slot_id + next_slot_step) % 60;
  }
}
#if 1
void task_lora_tx(void *arg)
{
  BaseType_t ret_code;
  msg_t lora_tx_msg = {.buf = NULL};
  uint32_t count = 0;
  uint16_t dataType;

  ulTaskNotifyTake(pdTRUE, portMAX_DELAY);

  while (1)
  {
    if (pdTRUE == xQueueReceive(global.queue_handle_lora_tx, &lora_tx_msg,
                                portMAX_DELAY))
    {
      if (lora_tx_msg.type != RADIO_TX)
      {
        continue;
      }
      NRF_LOG_INFO("[task_lora_tx] Send data length: %d", lora_tx_msg.buf_len);
      sx126x_tx(&global.sx1268, lora_tx_msg.buf, lora_tx_msg.buf_len, 55);

      ret_code = ulTaskNotifyTake(pdTRUE, ms2ticks(100));

      /* 根据等待通知的结果，执行对应操作 */
      if (ret_code == 1)
      {
        vTaskDelay(ms2ticks(100));
      }
      else
      {
        /* 超时返回  */
        NRF_LOG_INFO("SX1268 tx timeout.");
        sx126x_init(&global.sx1268);
        }
    
      sx126x_rx(&global.sx1268, 0);
    }
    if (lora_tx_msg.buf)
    {
      vPortFree(lora_tx_msg.buf);
      lora_tx_msg.buf = NULL;
    }
    
  }
}
#endif
void task_lora_tx_li(void *arg)
{
  BaseType_t ret_code;
  msg_t lora_tx_msg = {.buf = NULL};
  ulTaskNotifyTake(pdTRUE, portMAX_DELAY);
  for(;;)
  {
    if(pdTRUE == xQueueReceive (global.queue_handle_lora_tx, &lora_tx_msg, portMAX_DELAY))
    {
      if(lora_tx_msg.buf != NULL)
      {
        NRF_LOG_INFO("[task_lora_tx_li] Send data length: %d task_msg_p:%p   task_msg_buf: 0x%02x", lora_tx_msg.buf_len, lora_tx_msg.buf, lora_tx_msg.buf[0]);
        sx126x_tx(&global.sx1268, lora_tx_msg.buf, lora_tx_msg.buf_len, 55);
        ret_code = ulTaskNotifyTake(pdTRUE, ms2ticks(100));
        /* 根据等待通知的结果，执行对应操作 */
        if (ret_code == 1)
        {
          vTaskDelay(ms2ticks(2));
        }
        if(ret_code != 1)
        {
          NRF_LOG_INFO("SX1268 tx timeout.");
          sx126x_init(&global.sx1268);
        }
        if (lora_tx_msg.buf != NULL)
        {
          vPortFree(lora_tx_msg.buf);
          lora_tx_msg.buf = NULL;
          
        }
      }
       sx126x_rx(&global.sx1268, 0);
    }

  }
}
void task_lora_dio1_li(void *arg)
{
  msg_t msg;
  msg.buf = NULL;
  for(;;)
  {
    if(pdPASS == xQueueReceive (global.queue_lora_dio1_msg, &msg, portMAX_DELAY))
    {
      sx126x_dio1_process (&global.sx1268);
    }
  }
}
void task_lora_dio2_li(void *arg)
{
  msg_t msg;
  for(;;)
  {
    if(pdPASS == xQueueReceive (global.queue_lora_dio2_msg, &msg, portMAX_DELAY))
    {
      sx126x_dio2_process (&global.sx1268);
    }
  }
}

void task_lora_rx(void *arg)
{
  BaseType_t ret_code;
  msg_t lora_rx_msg = {.buf = NULL};
  /* 等待通知运行  */
  ulTaskNotifyTake(pdTRUE, portMAX_DELAY);

  while (1)
  {
    ret_code = xQueueReceive(global.queue_handle_lora_rx, &lora_rx_msg,
                             portMAX_DELAY);
    if (ret_code != pdTRUE)
    {
      continue;
    }
    NRF_LOG_ERROR("task_lora_rx:  receive packet length: %d", lora_rx_msg.buf_len);
    if (lora_rx_msg.type != RADIO_RX)
    {
      continue;
    }
    /* 扫描状态下接收到的数据包在else if下处理  */
    if (((get_byte_bit(global.lora_cmd, LORA_CMD_JOIN_BIT) == CMD_ENABLE) || (get_byte_bit(global.lora_cmd, LORA_CMD_ULDATA_BIT) == CMD_ENABLE)) && (get_byte_bit(global.lora_cmd, LORA_CMD_SCAN_BIT) == CMD_DISABLE))
    {
      dl_packet_handle(lora_rx_msg.buf, lora_rx_msg.buf_len, ((dl_sync_param_t *)lora_rx_msg.param)->rssi,
                       ((dl_sync_param_t *)lora_rx_msg.param)->snr);
    }
    else if (get_byte_bit(global.lora_cmd, LORA_CMD_SCAN_BIT) == CMD_ENABLE)
    {
      NRF_LOG_INFO("Scan phase:  receive packet type: %d", lora_rx_msg.type);

      /* 保存指定类型数据的SNR和频道号  */
      if ((lora_rx_msg.type == DL_SYNC0) || (lora_rx_msg.type == DL_SYNC1))
      {
        NRF_LOG_INFO("Scan ch: %d, rssi: %d, snr: %d.", global.scan_index,
                     ((dl_sync_param_t *)lora_rx_msg.param)->rssi, ((dl_sync_param_t *)lora_rx_msg.param)->snr);
        ch_scan_snr[global.scan_index].rssi_snr = ((dl_sync_param_t *)lora_rx_msg.param)->snr + ((dl_sync_param_t *)lora_rx_msg.param)->rssi;
        ch_scan_snr[global.scan_index].ch_index = global.scan_index;
        NRF_LOG_INFO("ch_scan_snr[%d].ch_index = %d", global.scan_index,
                     ch_scan_snr[global.scan_index].ch_index);
      }
    }
    if (lora_rx_msg.buf)
    {
      vPortFree(lora_rx_msg.buf);
      lora_rx_msg.buf = NULL;
    }
  }
}

```

