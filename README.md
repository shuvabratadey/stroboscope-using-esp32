# ⚡ ESP32 Stroboscope

<p align="center">
  <img src="https://raw.githubusercontent.com/shuvabratadey/stroboscope-using-esp32/main/Stroboscope%20Pictures/ESP32_Stroboscope.png" alt="ESP32 Stroboscope" width="700">
</p>

<p align="center">
  <b>A compact ESP32-based digital stroboscope for RPM measurement and rotating machinery inspection.</b>
</p>

---

## 📖 What is a Stroboscope?

A **stroboscope** is a device that produces a rapid series of light flashes at a controlled frequency.

When the flashing frequency is synchronized with the rotational speed of a moving object, the object can appear to be **stationary** or moving very slowly.

This phenomenon makes a stroboscope extremely useful for observing and analyzing rotating machinery without physically stopping it.

Typical objects that can be inspected include:

* Motors
* Fans
* Shafts
* Gears
* Pulleys
* Rotating assemblies

---

## 🚀 About This Project

This project is an **ESP32-based digital stroboscope** designed to generate high-speed flashes of light for observing and estimating the rotational speed of moving objects.

The ESP32 generates precisely controlled pulses that drive a **high-power LED through an N-channel MOSFET**.

The user can adjust the strobe parameters while monitoring the current values on an OLED display.

### Displayed Parameters

The display shows:

* 🔄 **RPM**
* ⚡ **Flash Frequency**
* 💡 **Duty Cycle**

By adjusting the flash frequency until the rotating object appears stationary, its rotational speed can be estimated.

---

## 🧮 RPM Calculation

The rotational speed can be calculated from the flashing frequency using:

```text
RPM = Frequency (Hz) × 60
```

### Example

If the strobe frequency is:

```text
1.67 Hz
```

Then:

```text
RPM = 1.67 × 60
RPM ≈ 100
```

Therefore, the rotating object is running at approximately:

```text
100 RPM
```

> **Note:** Harmonics and sub-harmonics can sometimes make the rotating object appear stationary at multiple flash frequencies. For accurate measurements, verify the fundamental rotational frequency whenever possible.

---

## 🛠️ Hardware

The project uses the following main components:

| Component                                 | Purpose                                 |
| ----------------------------------------- | --------------------------------------- |
| **ESP32**                                 | Main controller and pulse generation    |
| **High-Power LED**                        | Generates the stroboscopic flashes      |
| **N-Channel MOSFET**                      | Drives the high-current LED             |
| **OLED Display**                          | Displays RPM, frequency, and duty cycle |
| **Potentiometer / Rotary Control**        | User adjustment of strobe parameters    |
| **LED Driver / Current Limiting Circuit** | Controls LED current safely             |
| **Power Supply**                          | Powers the ESP32 and LED circuit        |
| **Enclosure**                             | Protects and houses the complete system |

---

## 🔌 System Block Diagram

```text
                    +--------------------+
                    |       ESP32        |
                    |                    |
                    |  Frequency Control |
                    |  Duty Cycle Control|
                    +---------+----------+
                              |
                              | PWM / Pulse
                              v
                       +-------------+
                       |   N-Channel |
                       |    MOSFET   |
                       +------+------+
                              |
                              v
                       +-------------+
                       | High-Power  |
                       |     LED     |
                       +-------------+
                              |
                              |
                       Flashing Light
                              |
                              v
                  +-----------------------+
                  |    Rotating Object    |
                  |                       |
                  | Motor / Fan / Shaft   |
                  |   Gear / Pulley       |
                  +-----------------------+
```

---

## 🎯 Applications

This stroboscope can be used for:

* 🔄 Motor RPM measurement
* 🌀 Fan speed inspection
* ⚙️ Gear and pulley inspection
* 🔧 Shaft rotation analysis
* 🏭 Rotating machinery analysis
* 🔍 Mechanical defect visualization
* 🧪 Laboratory experiments
* 🎓 Educational demonstrations
* 🛠️ Preventive maintenance and troubleshooting

## ▶️ YouTube Demo

<p align="center">
  <a href="https://youtube.com/shorts/vkFnO6racog?feature=share">
    <img src="https://img.youtube.com/vi/vkFnO6racog/maxresdefault.jpg" alt="ESP32 Stroboscope YouTube Demo" width="600">
  </a>
</p>

<p align="center">
  <a href="https://youtube.com/shorts/vkFnO6racog?feature=share">
    <b>▶ Watch the ESP32 Stroboscope Demo on YouTube</b>
  </a>
</p>

---

## 🧠 How It Works

The ESP32 generates a pulse signal at a user-selected frequency.

This signal switches the **N-channel MOSFET**, which rapidly turns the high-power LED ON and OFF.

When the LED flashing frequency matches the rotational frequency of the object, the object appears stationary because it is illuminated repeatedly at approximately the same angular position.

```text
ESP32
  |
  | Pulse Signal
  v
MOSFET
  |
  | High-Current Switching
  v
High-Power LED
  |
  | Stroboscopic Light
  v
Rotating Object
  |
  v
Object Appears Stationary
```

The frequency at this point can then be used to estimate the object's RPM.

---

## ⚠️ Safety

High-power LEDs can produce extremely bright flashes.

Please take appropriate precautions:

* Do not look directly into the LED.
* Avoid prolonged exposure to rapidly flashing light.
* Use appropriate current limiting for the LED.
* Ensure the MOSFET and LED driver are rated for the required current.
* Use proper thermal management for high-power LEDs.
* Disconnect power before modifying the circuit.

> ⚠️ **Photosensitive Epilepsy Warning:** Rapidly flashing lights may trigger seizures or discomfort in people with photosensitive epilepsy or other light sensitivities.

---

## 🕰️ Previous Prototype — ATmega328P

Before developing the ESP32 version, an earlier prototype of the stroboscope was built using an **ATmega328P-based implementation**.

The original project documentation is available here:

👉 [**View the ATmega328P Prototype Documentation**](https://github.com/shuvabratadey/stroboscope-using-esp32/blob/main/README_OLD.md)

## 💻 ESP32 Stroboscope Program

```
#include <stdio.h>
#include <stdint.h>
#include <stdbool.h>
#include <string.h>
#include <math.h>

#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

#include "esp_err.h"
#include "esp_log.h"

#include "driver/gpio.h"
#include "driver/i2c_master.h"
#include "driver/mcpwm_prelude.h"

#include "esp_adc/adc_oneshot.h"


/* ============================================================================
 * Pin definitions ==> Hardware Description
 * ============================================================================
 * GPIO32 -> Frequency coarse potentiometer
 * GPIO33 -> Frequency fine potentiometer
 * GPIO34 -> Duty-cycle potentiometer
 *
 * GPIO18 -> MOSFET gate / strobe PWM
 *
 * GPIO21 -> OLED SDA
 * GPIO22 -> OLED SCL
 */

#define STROBE_PWM_GPIO             GPIO_NUM_18

#define POT_FREQ_COARSE_GPIO        GPIO_NUM_32
#define POT_FREQ_FINE_GPIO          GPIO_NUM_33
#define POT_DUTY_GPIO               GPIO_NUM_34

#define I2C_SDA_GPIO                GPIO_NUM_21
#define I2C_SCL_GPIO                GPIO_NUM_22


/* ============================================================================
 * ADC channels
 *
 * Classic ESP32:
 *
 * GPIO32 = ADC1_CHANNEL_4
 * GPIO33 = ADC1_CHANNEL_5
 * GPIO34 = ADC1_CHANNEL_6
 * ============================================================================
 */

#define POT_FREQ_COARSE_CHANNEL     ADC_CHANNEL_4
#define POT_FREQ_FINE_CHANNEL       ADC_CHANNEL_5
#define POT_DUTY_CHANNEL            ADC_CHANNEL_6


/* ============================================================================
 * OLED configuration
 * SH1106 128x64 I2C
 * ============================================================================
 */

#define OLED_I2C_ADDRESS            0x3C

#define SCREEN_WIDTH                128
#define SCREEN_HEIGHT               64
#define OLED_PAGES                  (SCREEN_HEIGHT / 8)


/* ============================================================================
 * Frequency / duty configuration
 * Same values as your STM32 code
 * ============================================================================
 */

#define FREQ_COARSE_MIN             1.0f
#define FREQ_COARSE_MAX             300.0f

#define FREQ_FINE_SPAN              5.0f

#define FREQ_MIN                    1.0f
#define FREQ_MAX                    300.0f

#define DUTY_MIN                    1
#define DUTY_MAX                    99

#define EMA_ALPHA                   0.06f

#define ADC_OVERSAMPLE              32

#define FREQ_DEADBAND               0.10f
#define DUTY_DEADBAND               0.60f

#define DISPLAY_INTERVAL_MS         150


/* ============================================================================
 * MCPWM configuration
 *
 * 50 kHz timer resolution:
 *
 * 1 Hz   -> 50000 timer ticks
 * 300 Hz -> ~167 timer ticks
 *
 * The 50 kHz resolution is intentionally selected so the 1 Hz minimum
 * frequency remains within the MCPWM timer period range.
 * ============================================================================
 */

#define PWM_TIMER_RESOLUTION_HZ     50000UL


/* ============================================================================
 * Logging
 * ============================================================================
 */

static const char *TAG = "stroboscope";


/* ============================================================================
 * Peripheral handles
 * ============================================================================
 */

static adc_oneshot_unit_handle_t gAdcHandle;

static i2c_master_bus_handle_t gI2cBusHandle;
static i2c_master_dev_handle_t gOledHandle;

static mcpwm_timer_handle_t gPwmTimer;
static mcpwm_oper_handle_t gPwmOperator;
static mcpwm_cmpr_handle_t gPwmComparator;
static mcpwm_gen_handle_t gPwmGenerator;


/* ============================================================================
 * Runtime state
 * ============================================================================
 */

static float gEmaFrequency = 10.0f;
static float gEmaDuty = 50.0f;

static float gCurrentFrequency = 10.0f;
static uint8_t gCurrentDuty = 50;

static uint32_t gCurrentRpm = 600;


/* ============================================================================
 * OLED framebuffer
 * ============================================================================
 */

static uint8_t gOledBuffer[SCREEN_WIDTH * OLED_PAGES];


/* ============================================================================
 * Function prototypes
 * ============================================================================
 */

static float MapFloat(
    float x,
    float inMin,
    float inMax,
    float outMin,
    float outMax);

static float ClampFloat(
    float value,
    float minimum,
    float maximum);

static float ApplyDeadband(
    float incoming,
    float held,
    float deadband);

static void InitAdc(void);
static float ReadAnalogAveraged(adc_channel_t channel);

static void InitPwm(void);
static void ApplyPwm(float frequencyHz, uint8_t dutyPercent);

static void InitI2c(void);
static void InitOled(void);

static void OledSendCommand(uint8_t command);
static void OledUpdate(void);
static void OledClear(void);
static void OledSetPixel(int x, int y, bool state);
static void OledDrawHorizontalLine(int x, int y, int width);

static const uint8_t *GetGlyph(char character);

static void OledDrawChar(
    int x,
    int y,
    char character,
    uint8_t scale);

static void OledDrawString(
    int x,
    int y,
    const char *string,
    uint8_t scale);

static void ShowSplashScreen(void);
static void UpdateDisplay(void);

static void ReadControls(void);


/* ============================================================================
 * Small 5x7 font
 *
 * Only characters required by this application are included.
 * Each byte represents one vertical 7-pixel column.
 * ============================================================================
 */

static const uint8_t FONT_SPACE[] = {0x00, 0x00, 0x00, 0x00, 0x00};

static const uint8_t FONT_PERCENT[] = {0x63, 0x13, 0x08, 0x64, 0x63};
static const uint8_t FONT_DOT[]     = {0x00, 0x60, 0x60, 0x00, 0x00};
static const uint8_t FONT_COLON[]   = {0x00, 0x36, 0x36, 0x00, 0x00};

static const uint8_t FONT_0[] = {0x3E, 0x51, 0x49, 0x45, 0x3E};
static const uint8_t FONT_1[] = {0x00, 0x42, 0x7F, 0x40, 0x00};
static const uint8_t FONT_2[] = {0x42, 0x61, 0x51, 0x49, 0x46};
static const uint8_t FONT_3[] = {0x21, 0x41, 0x45, 0x4B, 0x31};
static const uint8_t FONT_4[] = {0x18, 0x14, 0x12, 0x7F, 0x10};
static const uint8_t FONT_5[] = {0x27, 0x45, 0x45, 0x45, 0x39};
static const uint8_t FONT_6[] = {0x3C, 0x4A, 0x49, 0x49, 0x30};
static const uint8_t FONT_7[] = {0x01, 0x71, 0x09, 0x05, 0x03};
static const uint8_t FONT_8[] = {0x36, 0x49, 0x49, 0x49, 0x36};
static const uint8_t FONT_9[] = {0x06, 0x49, 0x49, 0x29, 0x1E};

static const uint8_t FONT_A[] = {0x7E, 0x11, 0x11, 0x11, 0x7E};
static const uint8_t FONT_B[] = {0x7F, 0x49, 0x49, 0x49, 0x36};
static const uint8_t FONT_C[] = {0x3E, 0x41, 0x41, 0x41, 0x22};
static const uint8_t FONT_D[] = {0x7F, 0x41, 0x41, 0x22, 0x1C};
static const uint8_t FONT_E[] = {0x7F, 0x49, 0x49, 0x49, 0x41};
static const uint8_t FONT_F[] = {0x7F, 0x09, 0x09, 0x09, 0x01};
static const uint8_t FONT_H[] = {0x7F, 0x08, 0x08, 0x08, 0x7F};
static const uint8_t FONT_I[] = {0x00, 0x41, 0x7F, 0x41, 0x00};
static const uint8_t FONT_L[] = {0x7F, 0x40, 0x40, 0x40, 0x40};
static const uint8_t FONT_M[] = {0x7F, 0x02, 0x0C, 0x02, 0x7F};
static const uint8_t FONT_O[] = {0x3E, 0x41, 0x41, 0x41, 0x3E};
static const uint8_t FONT_P[] = {0x7F, 0x09, 0x09, 0x09, 0x06};
static const uint8_t FONT_R[] = {0x7F, 0x09, 0x19, 0x29, 0x46};
static const uint8_t FONT_S[] = {0x46, 0x49, 0x49, 0x49, 0x31};
static const uint8_t FONT_T[] = {0x01, 0x01, 0x7F, 0x01, 0x01};

static const uint8_t FONT_a[] = {0x20, 0x54, 0x54, 0x54, 0x78};
static const uint8_t FONT_e[] = {0x38, 0x54, 0x54, 0x54, 0x18};
static const uint8_t FONT_g[] = {0x0C, 0x52, 0x52, 0x52, 0x3E};
static const uint8_t FONT_i[] = {0x00, 0x44, 0x7D, 0x40, 0x00};
static const uint8_t FONT_l[] = {0x00, 0x41, 0x7F, 0x40, 0x00};
static const uint8_t FONT_n[] = {0x7C, 0x04, 0x04, 0x04, 0x78};
static const uint8_t FONT_q[] = {0x08, 0x14, 0x14, 0x18, 0x7C};
static const uint8_t FONT_r[] = {0x7C, 0x08, 0x04, 0x04, 0x08};
static const uint8_t FONT_t[] = {0x04, 0x3F, 0x44, 0x40, 0x20};
static const uint8_t FONT_u[] = {0x3C, 0x40, 0x40, 0x20, 0x7C};
static const uint8_t FONT_y[] = {0x0C, 0x50, 0x50, 0x50, 0x3C};
static const uint8_t FONT_z[] = {0x44, 0x64, 0x54, 0x4C, 0x44};


/* ============================================================================
 * Floating-point map
 * ============================================================================
 */

static float MapFloat(
    float x,
    float inMin,
    float inMax,
    float outMin,
    float outMax)
{
    if (inMax == inMin)
    {
        return outMin;
    }

    return ((x - inMin) *
            (outMax - outMin) /
            (inMax - inMin)) + outMin;
}


/* ============================================================================
 * Clamp
 * ============================================================================
 */

static float ClampFloat(
    float value,
    float minimum,
    float maximum)
{
    if (value < minimum)
    {
        return minimum;
    }

    if (value > maximum)
    {
        return maximum;
    }

    return value;
}


/* ============================================================================
 * Deadband
 * ============================================================================
 */

static float ApplyDeadband(
    float incoming,
    float held,
    float deadband)
{
    if (fabsf(incoming - held) >= deadband)
    {
        return incoming;
    }

    return held;
}


/* ============================================================================
 * ADC initialization
 * ============================================================================
 */

static void InitAdc(void)
{
    adc_oneshot_unit_init_cfg_t unitConfig =
    {
        .unit_id = ADC_UNIT_1,
    };

    ESP_ERROR_CHECK(
        adc_oneshot_new_unit(
            &unitConfig,
            &gAdcHandle));

    adc_oneshot_chan_cfg_t channelConfig =
    {
        .atten = ADC_ATTEN_DB_12,
        .bitwidth = ADC_BITWIDTH_12,
    };

    ESP_ERROR_CHECK(
        adc_oneshot_config_channel(
            gAdcHandle,
            POT_FREQ_COARSE_CHANNEL,
            &channelConfig));

    ESP_ERROR_CHECK(
        adc_oneshot_config_channel(
            gAdcHandle,
            POT_FREQ_FINE_CHANNEL,
            &channelConfig));

    ESP_ERROR_CHECK(
        adc_oneshot_config_channel(
            gAdcHandle,
            POT_DUTY_CHANNEL,
            &channelConfig));

    ESP_LOGI(TAG, "ADC initialized");
}


/* ============================================================================
 * ADC oversampling
 * ============================================================================
 */

static float ReadAnalogAveraged(adc_channel_t channel)
{
    uint32_t sum = 0;

    for (uint32_t sample = 0;
         sample < ADC_OVERSAMPLE;
         sample++)
    {
        int rawValue = 0;

        if (adc_oneshot_read(
                gAdcHandle,
                channel,
                &rawValue) == ESP_OK)
        {
            sum += (uint32_t)rawValue;
        }
    }

    return (float)sum / (float)ADC_OVERSAMPLE;
}


/* ============================================================================
 * MCPWM initialization
 * ============================================================================
 */

static void InitPwm(void)
{
    /*
     * Start with the same initial condition as the Arduino program:
     *
     * 10 Hz
     * 50% duty
     */

    uint32_t initialPeriod =
        (uint32_t)lroundf(
            (float)PWM_TIMER_RESOLUTION_HZ /
            gCurrentFrequency);

    mcpwm_timer_config_t timerConfig =
    {
        .group_id = 0,

        .clk_src = MCPWM_TIMER_CLK_SRC_DEFAULT,

        .resolution_hz = PWM_TIMER_RESOLUTION_HZ,

        .period_ticks = initialPeriod,

        .count_mode = MCPWM_TIMER_COUNT_MODE_UP,

        /*
         * Period updates occur cleanly at the next timer zero.
         */
        .flags.update_period_on_empty = true,
    };

    ESP_ERROR_CHECK(
        mcpwm_new_timer(
            &timerConfig,
            &gPwmTimer));


    mcpwm_operator_config_t operatorConfig =
    {
        .group_id = 0,
    };

    ESP_ERROR_CHECK(
        mcpwm_new_operator(
            &operatorConfig,
            &gPwmOperator));

    ESP_ERROR_CHECK(
        mcpwm_operator_connect_timer(
            gPwmOperator,
            gPwmTimer));


    mcpwm_comparator_config_t comparatorConfig =
    {
        .flags.update_cmp_on_tez = true,
    };

    ESP_ERROR_CHECK(
        mcpwm_new_comparator(
            gPwmOperator,
            &comparatorConfig,
            &gPwmComparator));


    mcpwm_generator_config_t generatorConfig =
    {
        .gen_gpio_num = STROBE_PWM_GPIO,
    };

    ESP_ERROR_CHECK(
        mcpwm_new_generator(
            gPwmOperator,
            &generatorConfig,
            &gPwmGenerator));


    /*
     * At timer = 0:
     * PWM goes HIGH.
     */
    ESP_ERROR_CHECK(
        mcpwm_generator_set_action_on_timer_event(
            gPwmGenerator,
            MCPWM_GEN_TIMER_EVENT_ACTION(
                MCPWM_TIMER_DIRECTION_UP,
                MCPWM_TIMER_EVENT_EMPTY,
                MCPWM_GEN_ACTION_HIGH)));


    /*
     * At comparator:
     * PWM goes LOW.
     */
    ESP_ERROR_CHECK(
        mcpwm_generator_set_action_on_compare_event(
            gPwmGenerator,
            MCPWM_GEN_COMPARE_EVENT_ACTION(
                MCPWM_TIMER_DIRECTION_UP,
                gPwmComparator,
                MCPWM_GEN_ACTION_LOW)));


    uint32_t compareValue =
        (initialPeriod * gCurrentDuty) / 100U;

    ESP_ERROR_CHECK(
        mcpwm_comparator_set_compare_value(
            gPwmComparator,
            compareValue));


    ESP_ERROR_CHECK(
        mcpwm_timer_enable(gPwmTimer));

    ESP_ERROR_CHECK(
        mcpwm_timer_start_stop(
            gPwmTimer,
            MCPWM_TIMER_START_NO_STOP));

    ESP_LOGI(
        TAG,
        "PWM initialized: %.1f Hz, %u%%",
        gCurrentFrequency,
        gCurrentDuty);
}


/* ============================================================================
 * Update PWM frequency and duty
 * ============================================================================
 */

static void ApplyPwm(
    float frequencyHz,
    uint8_t dutyPercent)
{
    if (frequencyHz < FREQ_MIN)
    {
        frequencyHz = FREQ_MIN;
    }

    if (frequencyHz > FREQ_MAX)
    {
        frequencyHz = FREQ_MAX;
    }

    if (dutyPercent < DUTY_MIN)
    {
        dutyPercent = DUTY_MIN;
    }

    if (dutyPercent > DUTY_MAX)
    {
        dutyPercent = DUTY_MAX;
    }


    uint32_t periodTicks =
        (uint32_t)lroundf(
            (float)PWM_TIMER_RESOLUTION_HZ /
            frequencyHz);

    if (periodTicks < 2U)
    {
        periodTicks = 2U;
    }


    uint32_t compareTicks =
        (periodTicks * dutyPercent) / 100U;

    if (compareTicks == 0U)
    {
        compareTicks = 1U;
    }

    if (compareTicks >= periodTicks)
    {
        compareTicks = periodTicks - 1U;
    }


    ESP_ERROR_CHECK(
        mcpwm_timer_set_period(
            gPwmTimer,
            periodTicks));

    ESP_ERROR_CHECK(
        mcpwm_comparator_set_compare_value(
            gPwmComparator,
            compareTicks));
}


/* ============================================================================
 * I2C initialization
 * ============================================================================
 */

static void InitI2c(void)
{
    i2c_master_bus_config_t busConfig =
    {
        .i2c_port = I2C_NUM_0,

        .sda_io_num = I2C_SDA_GPIO,
        .scl_io_num = I2C_SCL_GPIO,

        .clk_source = I2C_CLK_SRC_DEFAULT,

        .glitch_ignore_cnt = 7,

        .flags.enable_internal_pullup = true,
    };

    ESP_ERROR_CHECK(
        i2c_new_master_bus(
            &busConfig,
            &gI2cBusHandle));


    i2c_device_config_t deviceConfig =
    {
        .dev_addr_length = I2C_ADDR_BIT_LEN_7,

        .device_address = OLED_I2C_ADDRESS,

        .scl_speed_hz = 400000,
    };

    ESP_ERROR_CHECK(
        i2c_master_bus_add_device(
            gI2cBusHandle,
            &deviceConfig,
            &gOledHandle));

    ESP_LOGI(TAG, "I2C initialized");
}


/* ============================================================================
 * Send one SH1106 command
 * ============================================================================
 */

static void OledSendCommand(uint8_t command)
{
    uint8_t packet[2];

    /*
     * SH1106 I2C control byte:
     *
     * 0x00 = following byte is command
     */

    packet[0] = 0x00;
    packet[1] = command;

    ESP_ERROR_CHECK(
        i2c_master_transmit(
            gOledHandle,
            packet,
            sizeof(packet),
            100));
}


/* ============================================================================
 * Initialize SH1106
 * ============================================================================
 */

static void InitOled(void)
{
    vTaskDelay(pdMS_TO_TICKS(100));

    OledSendCommand(0xAE);       // Display OFF

    OledSendCommand(0xD5);       // Clock divide
    OledSendCommand(0x80);

    OledSendCommand(0xA8);       // Multiplex
    OledSendCommand(0x3F);

    OledSendCommand(0xD3);       // Display offset
    OledSendCommand(0x00);

    OledSendCommand(0x40);       // Start line

    OledSendCommand(0xAD);       // DC-DC control
    OledSendCommand(0x8B);

    OledSendCommand(0xA1);       // Segment remap

    OledSendCommand(0xC8);       // COM scan direction

    OledSendCommand(0xDA);
    OledSendCommand(0x12);

    OledSendCommand(0x81);       // Contrast
    OledSendCommand(0x80);

    OledSendCommand(0xD9);
    OledSendCommand(0x22);

    OledSendCommand(0xDB);
    OledSendCommand(0x35);

    OledSendCommand(0xA4);       // Display follows RAM
    OledSendCommand(0xA6);       // Normal display

    OledSendCommand(0xAF);       // Display ON

    OledClear();
    OledUpdate();

    ESP_LOGI(TAG, "SH1106 OLED initialized");
}


/* ============================================================================
 * Clear framebuffer
 * ============================================================================
 */

static void OledClear(void)
{
    memset(
        gOledBuffer,
        0,
        sizeof(gOledBuffer));
}


/* ============================================================================
 * Set framebuffer pixel
 * ============================================================================
 */

static void OledSetPixel(
    int x,
    int y,
    bool state)
{
    if ((x < 0) ||
        (x >= SCREEN_WIDTH) ||
        (y < 0) ||
        (y >= SCREEN_HEIGHT))
    {
        return;
    }

    uint32_t index =
        (uint32_t)x +
        ((uint32_t)(y / 8) * SCREEN_WIDTH);

    uint8_t mask =
        (uint8_t)(1U << (y & 7));


    if (state)
    {
        gOledBuffer[index] |= mask;
    }
    else
    {
        gOledBuffer[index] &= (uint8_t)~mask;
    }
}


/* ============================================================================
 * Horizontal line
 * ============================================================================
 */

static void OledDrawHorizontalLine(
    int x,
    int y,
    int width)
{
    for (int i = 0; i < width; i++)
    {
        OledSetPixel(
            x + i,
            y,
            true);
    }
}


/* ============================================================================
 * Copy framebuffer to SH1106
 * ============================================================================
 */

static void OledUpdate(void)
{
    /*
     * SH1106 has a 132-column internal RAM.
     *
     * The common 128x64 modules normally use columns 2..129,
     * hence the +2 offset.
     */

    uint8_t packet[SCREEN_WIDTH + 1];

    packet[0] = 0x40;


    for (uint8_t page = 0;
         page < OLED_PAGES;
         page++)
    {
        OledSendCommand(
            (uint8_t)(0xB0 | page));

        /*
         * Column = 2
         */

        OledSendCommand(0x02);
        OledSendCommand(0x10);


        memcpy(
            &packet[1],
            &gOledBuffer[
                page * SCREEN_WIDTH],
            SCREEN_WIDTH);


        ESP_ERROR_CHECK(
            i2c_master_transmit(
                gOledHandle,
                packet,
                sizeof(packet),
                100));
    }
}


/* ============================================================================
 * Font lookup
 * ============================================================================
 */

static const uint8_t *GetGlyph(char character)
{
    switch (character)
    {
        case ' ': return FONT_SPACE;

        case '%': return FONT_PERCENT;
        case '.': return FONT_DOT;
        case ':': return FONT_COLON;

        case '0': return FONT_0;
        case '1': return FONT_1;
        case '2': return FONT_2;
        case '3': return FONT_3;
        case '4': return FONT_4;
        case '5': return FONT_5;
        case '6': return FONT_6;
        case '7': return FONT_7;
        case '8': return FONT_8;
        case '9': return FONT_9;

        case 'A': return FONT_A;
        case 'B': return FONT_B;
        case 'C': return FONT_C;
        case 'D': return FONT_D;
        case 'E': return FONT_E;
        case 'F': return FONT_F;
        case 'H': return FONT_H;
        case 'I': return FONT_I;
        case 'L': return FONT_L;
        case 'M': return FONT_M;
        case 'O': return FONT_O;
        case 'P': return FONT_P;
        case 'R': return FONT_R;
        case 'S': return FONT_S;
        case 'T': return FONT_T;

        case 'a': return FONT_a;
        case 'e': return FONT_e;
        case 'g': return FONT_g;
        case 'i': return FONT_i;
        case 'l': return FONT_l;
        case 'n': return FONT_n;
        case 'q': return FONT_q;
        case 'r': return FONT_r;
        case 't': return FONT_t;
        case 'u': return FONT_u;
        case 'y': return FONT_y;
        case 'z': return FONT_z;

        default:
            return FONT_SPACE;
    }
}


/* ============================================================================
 * Draw one character
 * ============================================================================
 */

static void OledDrawChar(
    int x,
    int y,
    char character,
    uint8_t scale)
{
    const uint8_t *glyph =
        GetGlyph(character);


    for (int column = 0;
         column < 5;
         column++)
    {
        uint8_t columnData =
            glyph[column];


        for (int row = 0;
             row < 7;
             row++)
        {
            if (columnData & (1U << row))
            {
                for (int scaleX = 0;
                     scaleX < scale;
                     scaleX++)
                {
                    for (int scaleY = 0;
                         scaleY < scale;
                         scaleY++)
                    {
                        OledSetPixel(
                            x +
                            (column * scale) +
                            scaleX,

                            y +
                            (row * scale) +
                            scaleY,

                            true);
                    }
                }
            }
        }
    }
}


/* ============================================================================
 * Draw string
 * ============================================================================
 */

static void OledDrawString(
    int x,
    int y,
    const char *string,
    uint8_t scale)
{
    while (*string != '\0')
    {
        OledDrawChar(
            x,
            y,
            *string,
            scale);

        /*
         * 5 pixels character + 1 pixel spacing.
         */

        x += 6 * scale;

        string++;
    }
}


/* ============================================================================
 * Splash screen
 * ============================================================================
 */

static void ShowSplashScreen(void)
{
    OledClear();

    OledDrawString(
        8,
        16,
        "ESP32 LED",
        1);

    OledDrawString(
        8,
        30,
        "STROBOSCOPE",
        1);

    OledDrawString(
        8,
        46,
        "Initializing...",
        1);

    OledUpdate();

    vTaskDelay(
        pdMS_TO_TICKS(1200));

    OledClear();
    OledUpdate();
}


/* ============================================================================
 * Read potentiometers
 * ============================================================================
 */

static void ReadControls(void)
{
    /*
     * 1. Oversample all three ADC channels.
     */

    float rawCoarse =
        ReadAnalogAveraged(
            POT_FREQ_COARSE_CHANNEL);

    float rawFine =
        ReadAnalogAveraged(
            POT_FREQ_FINE_CHANNEL);

    float rawDuty =
        ReadAnalogAveraged(
            POT_DUTY_CHANNEL);


    /*
     * Your original STM32 frequency pots are intentionally reversed:
     *
     * ADC 4095 -> minimum
     * ADC 0    -> maximum
     */

    float coarseHz =
        MapFloat(
            rawCoarse,
            4095.0f,
            0.0f,
            FREQ_COARSE_MIN,
            FREQ_COARSE_MAX);


    float fineHz =
        MapFloat(
            rawFine,
            4095.0f,
            0.0f,
            -FREQ_FINE_SPAN,
            FREQ_FINE_SPAN);


    float targetFrequency =
        ClampFloat(
            coarseHz + fineHz,
            FREQ_MIN,
            FREQ_MAX);


    float targetDuty =
        MapFloat(
            rawDuty,
            0.0f,
            4095.0f,
            DUTY_MIN,
            DUTY_MAX);


    /*
     * 2. EMA low-pass filtering.
     */

    gEmaFrequency +=
        EMA_ALPHA *
        (targetFrequency - gEmaFrequency);

    gEmaDuty +=
        EMA_ALPHA *
        (targetDuty - gEmaDuty);


    /*
     * 3. Round frequency to 0.1 Hz
     *    and duty to 1%.
     */

    float roundedFrequency =
        roundf(
            gEmaFrequency * 10.0f) /
        10.0f;

    float roundedDuty =
        roundf(gEmaDuty);


    /*
     * 4. Deadband.
     */

    gCurrentFrequency =
        ApplyDeadband(
            roundedFrequency,
            gCurrentFrequency,
            FREQ_DEADBAND);


    float stableDuty =
        ApplyDeadband(
            roundedDuty,
            (float)gCurrentDuty,
            DUTY_DEADBAND);

    gCurrentDuty =
        (uint8_t)stableDuty;


    /*
     * Flashes/minute = Hz * 60.
     */

    gCurrentRpm =
        (uint32_t)lroundf(
            gCurrentFrequency * 60.0f);
}


/* ============================================================================
 * Main OLED screen
 * ============================================================================
 */

static void UpdateDisplay(void)
{
    char stringBuffer[32];

    OledClear();


    /* ------------------------------------------------------------------------
     * Header
     * ------------------------------------------------------------------------
     */

    OledDrawString(
        0,
        0,
        "STROBOSCOPE",
        1);

    OledDrawHorizontalLine(
        0,
        9,
        SCREEN_WIDTH);


    /* ------------------------------------------------------------------------
     * RPM label
     * ------------------------------------------------------------------------
     */

    OledDrawString(
        0,
        12,
        "RPM",
        1);


    /* ------------------------------------------------------------------------
     * Large RPM
     * ------------------------------------------------------------------------
     */

    snprintf(
        stringBuffer,
        sizeof(stringBuffer),
        "%lu",
        (unsigned long)gCurrentRpm);


    int rpmLength =
        strlen(stringBuffer);

    /*
     * Each scale-3 character:
     *
     * 6 * 3 = 18 pixels wide
     */

    int rpmX =
        SCREEN_WIDTH -
        (rpmLength * 18);

    if (rpmX < 0)
    {
        rpmX = 0;
    }


    OledDrawString(
        rpmX,
        12,
        stringBuffer,
        3);


    OledDrawHorizontalLine(
        0,
        38,
        SCREEN_WIDTH);


    /* ------------------------------------------------------------------------
     * Frequency
     * ------------------------------------------------------------------------
     */

    OledDrawString(
        0,
        42,
        "Freq:",
        1);


    snprintf(
        stringBuffer,
        sizeof(stringBuffer),
        "%.1f Hz",
        gCurrentFrequency);

    OledDrawString(
        0,
        54,
        stringBuffer,
        1);


    /* ------------------------------------------------------------------------
     * Duty
     * ------------------------------------------------------------------------
     */

    OledDrawString(
        70,
        42,
        "Duty:",
        1);


    snprintf(
        stringBuffer,
        sizeof(stringBuffer),
        "%u %%",
        gCurrentDuty);

    OledDrawString(
        70,
        54,
        stringBuffer,
        1);


    OledUpdate();
}


/* ============================================================================
 * app_main
 * ============================================================================
 */

void app_main(void)
{
    ESP_LOGI(
        TAG,
        "ESP32 LED stroboscope starting");


    /*
     * Keep MOSFET gate LOW during initialization.
     */

    gpio_config_t outputConfig =
    {
        .pin_bit_mask =
            (1ULL << STROBE_PWM_GPIO),

        .mode = GPIO_MODE_OUTPUT,

        .pull_up_en =
            GPIO_PULLUP_DISABLE,

        .pull_down_en =
            GPIO_PULLDOWN_ENABLE,

        .intr_type =
            GPIO_INTR_DISABLE,
    };

    ESP_ERROR_CHECK(
        gpio_config(
            &outputConfig));

    gpio_set_level(
        STROBE_PWM_GPIO,
        0);


    /*
     * Initialize peripherals.
     */

    InitAdc();

    InitI2c();

    InitOled();

    ShowSplashScreen();

    InitPwm();


    TickType_t lastDisplayUpdate =
        xTaskGetTickCount();


    while (true)
    {
        ReadControls();

        ApplyPwm(
            gCurrentFrequency,
            gCurrentDuty);


        TickType_t now =
            xTaskGetTickCount();


        if ((now - lastDisplayUpdate) >=
            pdMS_TO_TICKS(
                DISPLAY_INTERVAL_MS))
        {
            lastDisplayUpdate = now;

            UpdateDisplay();
        }


        /*
         * Small task delay.
         *
         * ADC oversampling already consumes some processing time,
         * so 5 ms is sufficient here.
         */

        vTaskDelay(
            pdMS_TO_TICKS(5));
    }
}
```
