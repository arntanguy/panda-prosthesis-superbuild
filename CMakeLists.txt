option(WITH_PANDA_BRACE "Build the PandaBrace robot modules and controller" ON)

if(WITH_PANDA_BRACE)
CreateCatkinWorkspace(ID panda_prosthesis_ws DIR panda/catkin_ws_panda_prosthesis CATKIN_BUILD)

AddCatkinProject(brace_top_setup
  GITHUB arntanguy/brace_top_setup 
  GIT_TAG origin/main
  WORKSPACE panda_prosthesis_ws
)

AddCatkinProject(brace_bottom_setup
  GITHUB arntanguy/brace_bottom_setup 
  GIT_TAG origin/main
  WORKSPACE panda_prosthesis_ws
)

AddProject(mc_rtc_ati_daq
  SUBFOLDER panda
  GITE multi-contact/mc_rtc_ati_daq
  GIT_TAG origin/topic/multisensor
  NO_NINJA
  DEPENDS mc_rtc
)
endif()

AddProject(panda_prosthesis
  SUBFOLDER panda
  GITHUB arntanguy/panda_prosthesis
  GIT_TAG origin/topic/V2
  DEPENDS mc_rtc mc_panda
)
