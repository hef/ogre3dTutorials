

project(tutorial)

#Allow for debugging on windows
if(WIN32)
#Maybe I should replace this with CMAKE_COMMAND copy.
configure_file(${OGRE_BINARY_DBG} "${PROJECT_BINARY_DIR}/" COPYONLY)
configure_file(${OGRE_BINARY_REL} "${PROJECT_BINARY_DIR}/" COPYONLY)
configure_file(${OIS_BINARY_DBG} "${PROJECT_BINARY_DIR}/" COPYONLY)
configure_file(${OIS_BINARY_REL} "${PROJECT_BINARY_DIR}/" COPYONLY)
endif(WIN32)

add_executable(tutorial WIN32
		TutorialApplication.cpp
		TutorialApplication.h
		BaseApplication.cpp
		BaseApplication.h)
#TODO: Create these files in the subproject specific location
configure_file(${CMAKE_SOURCE_DIR}/config/plugins.cfg.in ${PROJECT_BINARY_DIR}/plugins.cfg)
configure_file(resources.cfg.in ${PROJECT_BINARY_DIR}/resources.cfg)
TARGET_LINK_LIBRARIES(tutorial
	${OGRE_LIBRARIES}
	${OIS_LIBRARIES}
	${BOOST_LIBRIRIES}
	)
