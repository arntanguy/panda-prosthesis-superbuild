option(WITH_PANDA_BRACE "Build the PandaBrace robot modules and controller" ON)
option(WITH_TACTILE_FORCE_SENSOR_PLUGIN "Build the Tactile Force Sensor Plugin" OFF)
option(WITH_MC_RTC_ATI_DAQ "Build with ATI acquisition card" OFF)
option(WITH_PHIDGET_PRESSURE_SENSOR_PLUGIN "Build with Phidget Pressure Sensor Plugin" OFF)

if(WITH_PANDA_BRACE)
CreateCatkinWorkspace(ID panda_prosthesis_ws DIR panda/catkin_ws_panda_prosthesis CATKIN_BUILD)

AddCatkinProject(brace_top_setup
  GITHUB_PRIVATE arntanguy/brace_top_setup
  GIT_TAG origin/main
  WORKSPACE panda_prosthesis_ws
)

AddCatkinProject(brace_top_setup_brace
  GITHUB_PRIVATE arntanguy/brace_top_setup_brace
  GIT_TAG origin/main
  WORKSPACE panda_prosthesis_ws
)

AddCatkinProject(brace_bottom_setup
  GITHUB_PRIVATE arntanguy/brace_bottom_setup
  GIT_TAG origin/main
  WORKSPACE panda_prosthesis_ws
)

AddCatkinProject(brace_urdf
  GITHUB_PRIVATE arntanguy/brace_urdf
  GIT_TAG origin/main
  WORKSPACE panda_prosthesis_ws
)
endif()

if(WITH_MC_RTC_ATI_DAQ)
AddProject(mc_rtc_ati_daq
  SUBFOLDER panda
  GITE multi-contact/mc_rtc_ati_daq
  GIT_TAG origin/topic/multisensor
  NO_NINJA
  NO_SOURCE_MONITOR
  DEPENDS mc_rtc
  CMAKE_ARGS -DAUTOLOAD_AtiDaq_PLUGIN=OFF
)
endif()

AddProject(panda_prosthesis
  SUBFOLDER panda
  GITHUB_PRIVATE arntanguy/panda_prosthesis
  GIT_TAG origin/topic/V2
  DEPENDS mc_rtc mc_panda
)

if(WITH_TACTILE_FORCE_SENSOR_PLUGIN)
AptInstall(libbluetooth-dev)
AptInstall(ros-${ROS_DISTRO}-serial)
AddProject(tactile_force_sensor_plugin
  SUBFOLDER panda
  GITE azermane/tactileforcesensorplugin
  GIT_TAG origin/main
  DEPENDS mc_rtc
  CMAKE_ARGS -DAUTOLOAD_TFSPlugin_PLUGIN=OFF
)
endif()

if(WITH_PHIDGET_PRESSURE_SENSOR_PLUGIN)
  AptInstall(sharutils)
  set (PHIDGET_URL "https://www.phidgets.com/downloads/phidget22/libraries/linux/libphidget22.tar.gz")
  message(STATUS "PHIDGET -- Downloading SDK archive")
  DownloadFile("${PHIDGET_URL}" "${CMAKE_CURRENT_BINARY_DIR}/phidget/phidget.tar.gz" "")
  file(ARCHIVE_EXTRACT
        INPUT "${CMAKE_CURRENT_BINARY_DIR}/phidget/phidget.tar.gz"
        DESTINATION "${CMAKE_CURRENT_BINARY_DIR}/phidget"
  )
  set (PHIDGET_DIR "${CMAKE_CURRENT_BINARY_DIR}/phidget/libphidget22-1.15.20230616")

  execute_process(
    WORKING_DIRECTORY "${PHIDGET_DIR}"
    COMMAND "./configure --prefix=${CMAKE_INSTALL_PREFIX} && make && make install"
  )

  AddProject(mc_phidget_pressure_sensor
    SUBFOLDER panda
    GITHUB arntanguy/mc_phidget_pressure_sensor
    GIT_TAG origin/main
    DEPENDS mc_rtc
  )
endif()
