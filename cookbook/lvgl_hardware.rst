.. _lvgl-cookbook:

LVGL: Displays and Touch screens
================================

.. seo::
    :description: Recipes for common hardware platforms used for LVGL Displays with ESPHome
    :image: /images/lvgl.png

Here is the hardware configuration boilerplate you need to use :doc:`/components/lvgl/index` in ESPHome on various popular platforms.

Lanbon L8
---------

.. _lvgl-cookbook-lanbon-l8:

.. note::

    Choose a model that works with Apple HomeKit because those are WiFi versions with the internal antenna connected to the ESP32. Do NOT buy a version that is powered by Tuya Smart Life because the internal antenna is connected to the Tuya chip.

.. code-block:: yaml

    psram:
      mode: octal
      speed: 80MHz
    
    output:
      - platform: ledc
        pin: GPIO5
        id: backlight_pwm
      - platform: ledc
        pin: GPIO26
        id: moodRed
      - platform: ledc
        pin: GPIO32
        id: moodGreen
      - platform: ledc
        pin: GPIO33
        id: moodBlue
      - platform: gpio
        pin: GPIO12
        id: relay_1
      - platform: gpio
        pin: GPIO14
        id: relay_2
      - platform: gpio
        pin: GPIO27
        id: relay_3
    
    light:
      - platform: rgb
        name: "Mood Light"
        red: moodRed
        green: moodGreen
        blue: moodBlue
      - platform: monochromatic
        name: "Backlight"
        id: backlight
        output: backlight_pwm
        restore_mode: ALWAYS_ON
    
    spi:
      clk_pin: GPIO19
      mosi_pin: GPIO23
      miso_pin: GPIO25
    
    i2c:
      sda: GPIO4
      scl: GPIO0
    
    display:
      - id: langbon_L8
        platform: ili9xxx
        model: ST7789V
        invert_colors: false
        dimensions: 240x320
        cs_pin: GPIO22
        dc_pin: GPIO21
        reset_pin: GPIO18
        auto_clear_enabled: false
        update_interval: never
        rotation: 180
    
    touchscreen:
      platform: ft63x6
      calibration:
        x_min: 0
        y_min: 0
        x_max: 230
        y_max: 312
      on_release:
        - if:
            condition: lvgl.is_paused
            then:
              - lvgl.resume:
              - lvgl.widget.redraw:
              - light.turn_on: backlight
    
    lvgl:
      on_idle:
        timeout: !lambda "return 10000;"
        then:
          - light.turn_off: backlight
          - lvgl.pause:
      pages:
         ...

See Also
--------

- :doc:`/components/lvgl/index`
- :doc:`/components/display/index`
- :doc:`/components/touchscreen/index`

- :ghedit:`Edit`
