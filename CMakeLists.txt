CreateCatkinWorkspace(ID panda_prosthesis_ws DIR panda/catkin_ws_panda_prosthesis CATKIN_BUILD)

AddCatkinProject(brace_top_setup
  GITHUB arntanguy/brace_top_setup 
  GIT_TAG main
  WORKSPACE panda_prosthesis_ws
)

AddCatkinProject(brace_bottom_setup
  GITHUB arntanguy/brace_bottom_setup 
  GIT_TAG main
  WORKSPACE panda_prosthesis_ws
)

AddProject(panda_prosthesis
  SUBFOLDER panda
  GITHUB arntanguy/panda_prosthesis
  GIT_TAG topic/V2
  DEPENDS mc_rtc mc_panda
)
