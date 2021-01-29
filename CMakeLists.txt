################################################################################
# Para mas información de este archivo visite la pagina de cmake:
# https://cmake.org/documentation/
################################################################################

# Establece la versión mínima requerida de cmake para un proyecto.
cmake_minimum_required(VERSION 3.0.2)

# Establece un nombre para el proyecto
project (latino-json)

# Desactivar algunas opciones para el build de Jansson
set(JANSSON_BUILD_DOCS OFF)
set(JANSSON_WITHOUT_TESTS ON)
set(JANSSON_INSTALL OFF)

if(WIN32)
  # Arquitectura del build
  set(TARGET_ARCH_REGEX "^.*-march[= ]([^ ]+).*$")
  string(REGEX MATCH "${TARGET_ARCH_REGEX}" TARGET_ARCH_MATCH ${CMAKE_C_FLAGS} ${CMAKE_CXX_FLAGS})
  if(TARGET_ARCH_MATCH)
      string(REGEX REPLACE "${TARGET_ARCH_REGEX}" "\\1" TARGET_ARCH ${CMAKE_C_FLAGS} ${CMAKE_CXX_FLAGS})
  else()
      set(TARGET_ARCH  ${CMAKE_HOST_SYSTEM_PROCESSOR})
  endif()

  # Establecer paths donde se instalo latino
  set(LATINO_PATH "C:/Program Files/Latino/bin" CACHE FILEPATH "Ruta de instalacion de latino.exe")
  set(LATINO_LIBC "C:/Program Files/Latino/libc" CACHE FILEPATH "Ruta de instalacion de latino.dll")
  set(LATINO_INCLUDE "C:/Program Files/Latino/include" CACHE FILEPATH "Ruta de instalacion de latino/include")

  # Enlazar
  include_directories(${LATINO_INCLUDE})
  link_directories(${LATINO_LIBC})

  message(STATUS "TARGET_ARCH: ${TARGET_ARCH}")
  message(STATUS "LATINO_PATH: ${LATINO_PATH}")
  message(STATUS "LATINO_INCLUDE: ${LATINO_INCLUDE}")
  message(STATUS "LATINO_LIBC: ${LATINO_LIBC}")
endif()

#Agrega un subdirectorio a la compilación.
add_subdirectory(jansson)

# Añade directorios de inclusión a la compilación
include_directories(jansson/include)
include_directories(jansson/src)
include_directories(include)

# Agrega una biblioteca al proyecto utilizando los
# archivos de origen especificados.
add_library(latino-json SHARED src/latino-json.c)

# Vincula un objetivo a bibliotecas determinadas.
target_link_libraries(latino-json jansson latino)

# Especifica las reglas que se ejecutarán en el momento de la instalación.
if(WIN32)
  install(TARGETS latino-json DESTINATION ${LATINO_LIBC})
else()
  install(FILES liblatino-json.so DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
endif()