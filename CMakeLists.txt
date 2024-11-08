cmake_minimum_required(VERSION 3.20)

project(setting-up-bullet-physics-from-cmakelists-opengles20-sdl3-cpp)
# USE the WIN32 key to hide the console like this:
# add_executable(app WIN32)
add_executable(app)

# Check for WebAssembly
if (CMAKE_SYSTEM_NAME MATCHES "Emscripten")

    target_include_directories(app PRIVATE "E:/libs/bullet3-2.83.7-prefix/web/include/bullet")

    target_sources(app
    PRIVATE
        src/main.cpp
    )

    find_package(SDL3)
    target_link_libraries(app PRIVATE
        "E:/libs/bullet3-2.83.7-prefix/web/lib/libBulletDynamics.a"
        "E:/libs/bullet3-2.83.7-prefix/web/lib/libBulletCollision.a"
        "E:/libs/bullet3-2.83.7-prefix/web/lib/libLinearMath.a"
        SDL3::SDL3)
endif()

# Check for Windows
if (WIN32)
    target_include_directories(app PRIVATE "E:/libs/bullet3-2.83.7-prefix/win/include/bullet")
    target_include_directories(app PRIVATE E:/libs/glad-2.0.6/include)

    target_sources(app
    PRIVATE
        E:/libs/glad-2.0.6/src/glad.c
        src/main.cpp
    )

    # This code copies the "SDL3.dll" file to the build directory
    add_custom_command(TARGET app POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        $<TARGET_RUNTIME_DLLS:app> $<TARGET_FILE_DIR:app>
    )

    find_package(SDL3)
    target_link_libraries(app PRIVATE
        "E:/libs/bullet3-2.83.7-prefix/win/lib/libBulletDynamics.a"
        "E:/libs/bullet3-2.83.7-prefix/win/lib/libBulletCollision.a"
        "E:/libs/bullet3-2.83.7-prefix/win/lib/libLinearMath.a"
        SDL3::SDL3)

    #find_package(Bullet)
    #target_link_libraries(app PRIVATE bullet::bullet SDL3::SDL3)
    #target_link_libraries(app PRIVATE ${BULLET_LIBRARIES} SDL3::SDL3)

    target_link_options(app PRIVATE -static)
endif()

target_compile_definitions(app PRIVATE SDL_MAIN_USE_CALLBACKS)
