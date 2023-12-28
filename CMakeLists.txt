cmake_minimum_required(VERSION 3.12)

project(plotting_lib)

find_package(ament_cmake REQUIRED)

set(VENDOR_PATHS vendor/imgui vendor/implot vendor/imgui/backends)


file(GLOB IM_GUI_SOURCES "vendor/imgui/*.cpp")
file(GLOB_RECURSE IM_GUI_BACKENDS_SOURCES "vendor/imgui/backends/*glfw.cpp")
file(GLOB_RECURSE IM_PLOT_SOURCES "vendor/implot/*.cpp")
file(GLOB PACKAGE_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/vendor/imgui/backends/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/vendor/imgui/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/vendor/implot/*.h")

# Find packages (ImGui, ImPlot, GLFW, OpenGL)
find_package(glfw3 REQUIRED)

# Add your OpenGL library (e.g., OpenGL::GL)
find_package(OpenGL REQUIRED)

# Set the executable target
add_library(${PROJECT_NAME}
	${IM_GUI_SOURCES}
	${IM_GUI_BACKENDS_SOURCES}
	${IM_PLOT_SOURCES}
	vendor/imgui/backends/imgui_impl_opengl3.cpp)

# Include directories
target_include_directories(${PROJECT_NAME} PUBLIC
	$<BUILD_INTERFACE:	${CMAKE_CURRENT_SOURCE_DIR}/vendor/imgui
						${CMAKE_CURRENT_SOURCE_DIR}/vendor/imgui/backends
						${CMAKE_CURRENT_SOURCE_DIR}/vendor/implot>
	$<INSTALL_INTERFACE:include>
)

# Link libraries
target_link_libraries(${PROJECT_NAME} PUBLIC
    glfw
    OpenGL::GL
)

add_executable(test_exe src/main.cpp)

target_link_libraries(test_exe ${PROJECT_NAME})

ament_export_targets(${PROJECT_NAME}_Target HAS_LIBRARY_TARGET)

install(TARGETS ${PROJECT_NAME}
		EXPORT ${PROJECT_NAME}_Target
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include)
install(FILES ${PACKAGE_HEADERS}
		DESTINATION include)

ament_package()
