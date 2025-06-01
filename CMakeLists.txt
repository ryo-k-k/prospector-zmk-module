# Include common and local headers
zephyr_include_directories(include)
zephyr_include_directories(${APPLICATION_SOURCE_DIR}/include)

# Create a Zephyr library for this module
zephyr_library()

# If the Prospector adapter shield is enabled
if(CONFIG_SHIELD_PROSPECTOR_ADAPTER)

    # Add custom display driver and LVGL GUI components
    add_subdirectory(${ZEPHYR_CURRENT_MODULE_DIR}/drivers/display)
    add_subdirectory(${ZEPHYR_CURRENT_MODULE_DIR}/modules/lvgl)

    # Enable Caps Word behavior override if the device tree supports it
    if(CONFIG_DT_HAS_ZMK_BEHAVIOR_CAPS_WORD_ENABLED)
        zephyr_library_sources(src/events/caps_word_state_changed.c)

        # Mark behavior_caps_word.c as header-only for app context (for clarity in IDEs)
        set_source_files_properties(
            ${APPLICATION_SOURCE_DIR}/src/behaviors/behavior_caps_word.c
            TARGET_DIRECTORY app
            PROPERTIES HEADER_FILE_ONLY ON
        )

        # Link the custom behavior source
        target_sources(app PRIVATE src/behaviors/behavior_caps_word.c)
    endif()

    # Split central communication handling
    zephyr_library_sources(src/events/split_central_status_changed.c)

    # Make the central.c file visible in IDEs even if already compiled elsewhere
    set_source_files_properties(
        ${APPLICATION_SOURCE_DIR}/src/split/bluetooth/central.c
        TARGET_DIRECTORY app
        PROPERTIES HEADER_FILE_ONLY ON
    )

    zephyr_library_sources(src/split/bluetooth/central.c)

endif()
